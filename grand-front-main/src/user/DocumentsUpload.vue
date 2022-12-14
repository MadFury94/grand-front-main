<script lang="ts" setup>
import { $axios } from "../http/http.Service";
import { computed, onMounted, ref } from "vue";
import { useRoute } from "vue-router";
import { CheckIcon, SelectorIcon } from "@heroicons/vue/solid";
import {
  Listbox,
  ListboxButton,
  ListboxOption,
  ListboxOptions,
} from "@headlessui/vue";
import BusyButton from "../components/BusyButton.vue";
import { fetchProfile } from "../http/account.Service";
import AllDocumentsComponent from "./AllDocumentsComponent.vue";
import LoadingComponent from "../admin/views/LoadingComponent.vue";

const documentCategory = [
  { title: "Application Form" },
  { title: "CV" },
  { title: "Language  Certificate" },
  { title: "Letter Of Motivation" },
  { title: "Other" },
  { title: "Passport" },
  { title: "School Certificate" },
  { title: "Study Certificate" },
  { title: "Work experience /Internship " },
  { title: "Work Sample" },
];

const selected = ref(documentCategory[4]);
const route = useRoute();
const appUrl = import.meta.env.VITE_API_URL;
const code = computed(() => route.params.referenceId);
const imageData = new FormData();
const imageInput = ref<HTMLInputElement>();
const url = ref("");
const file = ref("") as any;
const isLoaded = ref(false);

function changeImage() {
  imageInput.value?.click();
  // console.log(imageInput.value);
}

const { fetch, isLoading } = fetchProfile(route.params.referenceId);
onMounted(fetch);
function onFileChange(e: any) {
  file.value = e.target.files[0];

  url.value = URL.createObjectURL(file.value);
}

function uploadDocuments() {
  isLoaded.value = false;
  imageData.append("document", file.value);
  imageData.append("documentCategory", selected.value.title);

  if (!file.value) {
    console.log("no file");
  }

  $axios

    .patch(`/profile/upload/${code.value}/document`, imageData)

    .then((r) => {
      isLoaded.value = true;
      fetchProfile(route.params.referenceId).fetch();

      console.log(r);
    })

    .catch((e) => {
      isLoaded.value = true;

      return e;
    });
}
</script>

<template>
  <div>
    <div class="bg-yellow-200 border p-4 rounded-md">
      How you can help us evaluate your documents faster:
      <ul class="list-disc list-inside">
        <li>
          Please only upload
          <span class="text-sm font-medium">( .PDF, .JPG, .PNG, .DOC ) </span>
          files (max. 10 MB per document).
        </li>
        <li>
          Please upload a document with several pages in one PDF file and in the
          correct page order (e.g. School Transcript).
        </li>
        <li>
          Please upload different documents in different files (e.g. school
          certificate and copy of passport).
        </li>
        <li>
          Please upload each file only once and choose a suitable file name
          (e.g. School Certificate).
        </li>
      </ul>
    </div>

    <br />

    <div class="grid md:grid-cols-2 items-center gap-y-4 gap-x-20">
      <div>
        <div @click.prevent="changeImage">
          <div class="mt-1 flex rounded-md shadow-sm">
            <div
              class="relative flex items-stretch flex-grow focus-within:z-10"
            >
              <div
                class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none"
              >
                <i class="fal fa-folder-open"></i>
              </div>
              <input
                class="form-input-profile block w-full rounded-none rounded-l-md pl-10"
                value="Click to select a file "
                readonly
              />
            </div>
            <button
              type="button"
              class="-ml-px relative inline-flex items-center space-x-2 px-4 py-2 border border-gray-300 text-sm font-medium rounded-r-md text-gray-700 bg-gray-50 hover:bg-gray-100 focus:outline-none focus:ring-1 focus:ring-yellow-500 focus:border-yellow-500"
            >
              <span>Select</span>
            </button>
          </div>
        </div>
        <input
          hidden
          ref="imageInput"
          type="file"
          formenctype="multipart/form-data"
          @change.prevent="onFileChange"
        />
      </div>
      <div class="">
        <Listbox as="div" v-model="selected">
          <div class="mt-1 relative">
            <ListboxButton
              class="bg-white relative w-full border border-gray-300 rounded-md shadow-sm pl-3 pr-10 py-2 text-left cursor-default focus:outline-none focus:ring-1 focus:ring-yellow-500 focus:border-yellow-500 sm:text-sm"
            >
              <span class="block truncate">{{ selected.title }}</span>
              <span
                class="absolute inset-y-0 right-0 flex items-center pr-2 pointer-events-none"
              >
                <SelectorIcon
                  class="h-5 w-5 text-gray-400"
                  aria-hidden="true"
                />
              </span>
            </ListboxButton>

            <transition
              leave-active-class="transition ease-in duration-100"
              leave-from-class="opacity-100"
              leave-to-class="opacity-0"
            >
              <ListboxOptions
                class="absolute z-10 mt-1 w-full bg-white shadow-lg max-h-60 rounded-md py-1 text-base ring-1 ring-black ring-opacity-5 overflow-auto focus:outline-none sm:text-sm cursor-pointer"
              >
                <ListboxOption
                  as="template"
                  v-for="(item, index) in documentCategory"
                  :key="index"
                  :value="item"
                  v-slot="{ active, selected }"
                >
                  <li
                    :class="[
                      active ? 'text-white bg-yellow-600' : 'text-gray-900',
                      'cursor-default select-none relative py-2 pl-3 pr-9',
                    ]"
                  >
                    <span
                      :class="[
                        selected ? 'font-semibold' : 'font-normal',
                        'block truncate',
                      ]"
                    >
                      {{ item.title }}
                    </span>

                    <span
                      v-if="selected"
                      :class="[
                        active ? 'text-white' : 'text-yellow-600',
                        'absolute inset-y-0 right-0 flex items-center pr-4',
                      ]"
                    >
                      <CheckIcon class="h-5 w-5" aria-hidden="true" />
                    </span>
                  </li>
                </ListboxOption>
              </ListboxOptions>
            </transition>
          </div>
        </Listbox>
      </div>
    </div>
    <div class="flex justify-center">
      <BusyButton
        class="mt-3 btn bg-gray-700 w-40 py-2 mt-10"
        @click.prevent="uploadDocuments"
      >
        Upload Document
      </BusyButton>
    </div>

    <AllDocumentsComponent v-if="isLoading" />
    <LoadingComponent v-else />
  </div>
</template>
