<script setup lang="ts">
import Glide from "@glidejs/glide";
import {
  Images,
  Controls,
  Breakpoints,
} from "@glidejs/glide/dist/glide.modular.esm";
import { onMounted } from "vue";
import { ref } from "vue";
import DestinationCard from "./DestinationCard.vue";
import { appState } from "../store/store";
import dummyDestination from "../db/destination.json";

const root = ref(null);

const props = defineProps({
  promotedDestinations: {
    type: Array,
    default: () => [],
  },
  isLoaded: {
    type: Boolean,
    default: false,
  },
});
onMounted(() => {
  const slider = new Glide(".glide", {
    type: "carousel",
    perView: 3,
    focusAt: "center",
    autoplay: 3000,
    breakpoints: {
      767: {
        perView: 1,
      },
      991: {
        perView: 2,
      },
    },
  });
  slider.mount();
});
</script>

<template>
  <div class="">
    <div ref="root" class="glide">
      <!--      slider area-->
      <div class="glide__track" data-glide-el="track">
        <ul class="glide__slides">
          <li
            class="glide__slide"
            v-for="(item, index) in promotedDestinations"
            :key="index"
          >
            <DestinationCard :destination="item" />
          </li>
        </ul>
      </div>
      <!--      slider area-->

      <!--      arrow-->
      <div class="my-4">
        <div class="glide__arrows" data-glide-el="controls">
          <button
            class="glide__arrow glide__arrow--left invisible md:visible"
            data-glide-dir="<"
          >
            <i class="fas fa-chevron-left"></i>
          </button>
          <button
            class="glide__arrow glide__arrow--right invisible md:visible"
            data-glide-dir=">"
          >
            <i class="fas fa-chevron-right"></i>
          </button>
        </div>
      </div>
      <!--      arrow-->
    </div>
  </div>
</template>

<style scoped>
.glide__slide {
}

.glide__arrow {
  position: absolute;
  display: block;
  top: 50%;
  z-index: 2;
  color: black;
  text-transform: uppercase;
  background-color: rgba(187, 184, 184, 0.98);
  border-radius: 50%;
  border: none;
  box-shadow: none;
  text-shadow: 0 0.25em 0.5em rgb(0 0 0 / 10%);
  opacity: 50%;
  cursor: pointer;
  transition: opacity 150ms ease, border 300ms ease-in-out;
  transform: translateY(-50%);
  line-height: 1;
}
</style>
