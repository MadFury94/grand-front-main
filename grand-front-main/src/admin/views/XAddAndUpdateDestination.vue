<template>
  <div v-if="loadEditor" class="h-screen">
    <DestinationLinks />
    {{ singleDestinationStore.destination.promoted }}
    {{ singleDestinationStore.destination.enabled }}
    <template v-if="singleDestinationStore.isLoadingDeal">
      <form>
        <div class="grid lg:grid-cols-3">
          <div class="lg:col-span-2 mb-4 lg:mx-4">
            <div class="tileTab space-y-4 py-4">
              <div>
                <label class="form-label w-full">Title:</label>
                <div class="mt-1">
                  <input
                    v-model="singleDestinationStore.destination.title"
                    class="form-input"
                    type="text"
                  />
                </div>
              </div>
              <div class="editor-style">
                <label class="form-label">Description:</label>

                <Editor
                  :api-key="tiny.apiKey"
                  :init="{
                    height: 300,
                    menubar: false,
                    plugins: tiny.editorPlugins,
                    toolbar: tiny.editorToolbar,
                  }"
                  v-model="singleDestinationStore.destination.description"
                />
              </div>
              <div>
                <label class="form-label">Activity:</label>
                <div class="mt-1">
                  <input
                    v-model="singleDestinationStore.destination.activity"
                    class="form-input"
                    type="text"
                  />
                </div>
              </div>
              <div>
                <label class="form-label">Included:</label>
                <div class="editor-style">
                  <Editor
                    :api-key="tiny.apiKey"
                    :init="{
                      height: 300,

                      menubar: false,
                      plugins: tiny.editorPlugins,
                      toolbar: tiny.editorToolbar,
                    }"
                    v-model="singleDestinationStore.destination.included"
                  />
                </div>
              </div>
            </div>
          </div>
          <div>
            <div class="tileTab">
              <CountryLocatorSelector
                :destination="singleDestinationStore.destination"
              />

              <div class="pt-4">
                <div>
                  <DealTimePickerComponent
                    :destination="singleDestinationStore.destination"
                  />
                  <p class="text-sm">Click to edit date</p>
                </div>
                <div class="border-b py-4">
                  <label class="form-label">Price:</label>
                  <div class="mt-1">
                    <input
                      v-model="singleDestinationStore.destination.price"
                      class="form-input"
                      type="number"
                    />
                  </div>
                </div>

                <div class="border-b grid grid-cols-2">
                  <div>
                    <label class="my-4 font-medium">Promoted:</label>
                    <PromotedToggleComponent
                      :destination="singleDestinationStore.destination"
                    />
                  </div>
                  <div>
                    <label class="py-4 font-medium text-gray-700"
                      >Enable Destination:</label
                    >

                    <EnabledToggleComponent
                      :destination="singleDestinationStore.destination"
                    />
                  </div>
                </div>

                <div>
                  <button
                    class="bg-gray-700 p-2 text-white my-4 rounded-md"
                    @click.prevent="toggleModal"
                  >
                    Open Gallery
                  </button>
                  <div v-if="modalOpen">
                    <Modal @closeModal="toggleModal" max-size="max-w-5xl">
                      <GalleryComponent />
                    </Modal>
                  </div>

                  <DealSelectedImages />
                </div>

                <button
                  v-if="$route.name === 'AddDestination'"
                  @click.prevent="createDestination"
                  class="bg-yellow-600 hover:bg-yellow-500 w-full tracking-widest p-2 rounded-md mt-10"
                >
                  Create Destination
                </button>

                <BusyButton
                  v-else
                  class="primary-button-wide bg-yellow-500 tracking-widest"
                  :is-loading="isLoading"
                  @click.prevent="updateDestination"
                >
                  Update
                </BusyButton>
              </div>
            </div>
          </div>
        </div>
      </form>
    </template>
  </div>
</template>

<script lang="ts" setup>
import { useScriptTag } from "@vueuse/core";
import { computed, ref } from "vue";
import { $axios } from "../../http/http.Service";
import { useRoute, useRouter } from "vue-router";
import { DestinationType } from "../../types";
import DealTimePickerComponent from "../components/DealTimePickerComponent.vue";
import PromotedToggleComponent from "../components/PromotedToggleComponent.vue";
import CountryLocatorSelector from "../components/CountryLocatorSelector.vue";
import Editor from "@tinymce/tinymce-vue";

import {
  clearStore,
  getOneDestinationX,
  singleDestinationStore,
} from "../../store/destinationStore";
import DestinationLinks from "./DestinationLinks.vue";
import Gallery from "./Gallery.vue";
import DealSelectedImages from "./DealSelectedImages.vue";
import Modal from "../../components/Modal.vue";
import GalleryComponent from "../components/GalleryComponent.vue";
import BusyButton from "../../components/BusyButton.vue";
import { getPromotedDestination } from "../../http/client.Service";
import EnabledToggleComponent from "../components/EnabledToggleComponent.vue";

const tiny = {
  apiKey: import.meta.env.VITE_TINY_KEY,
  editorPlugins: [
    "advlist autolink lists link image charmap print preview anchor",
    "searchreplace visualblocks code fullscreen",
    "insertdatetime media table paste code help wordcount",
  ],
  editorToolbar: [
    "undo redo | formatselect | bold italic backcolor | \
             alignleft aligncenter alignright alignjustify | \
             bullist numlist outdent indent | removeformat | help |code",
  ],
};

const loadEditor = ref(false);

useScriptTag(
  "https://cdn.tiny.cloud/1/t5w054vk121zn69dg4rh0osw74sqokemdoedehp6oz381zpb/tinymce/5/tinymce.min.js",
  // on script tag loaded.
  (el: HTMLScriptElement) => {
    // do something

    loadEditor.value = true;
  }
);

const destination = ref(<DestinationType>{});

const isLoaded = ref(false),
  route = useRoute(),
  router = useRouter();

const modalOpen = ref(false);

destination.value = {} as DestinationType;

if (route.name === "UpdateDestination") {
  // getOneDestination();
  getOneDestinationX();
} else {
  clearStore();
  isLoaded.value = true;
  // window.location.href = "/add-destination/";
}

if (route.name === "AddDestination") {
  clearStore();
}

const code = computed(() => route.params.destinationId);
const isLoading = ref(false);

function updateDestination() {
  isLoading.value = true;

  getPromotedDestination();
  console.log(singleDestinationStore.destination, "update");
  $axios
    .patch(`manager/deals/${code.value}`, singleDestinationStore.destination)
    .then((r) => {
      isLoading.value = false;
      return r;
    })
    .catch((e) => {
      console.log(e, "??");
    });
}

function createDestination() {
  isLoading.value = true;

  $axios
    .post(`manager/destination/`, singleDestinationStore.destination)
    .then((r) => {
      isLoading.value = false;

      console.log(r);
    })
    .catch((r) => {
      return r;
    });
}

function toggleModal() {
  modalOpen.value = !modalOpen.value;
}
</script>

<style scoped>
.form-input {
  @apply bg-white w-full border border-gray-300 rounded-md py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-yellow-500;
}
.editor-style {
}
</style>
