<template>
  <div class="py-4">
    <div class="">
      <h2 class="title-2">Our Services</h2>
      <div class="sm:grid sm:grid-cols-2 xl:max-w-none lg:grid-cols-3 gap-4">
        <div v-for="item in services" class="my-10">
          <div class="bg-white rounded-lg shadow">
            <div>
              <!--              <button @click="showImage = !showImage">clissck</button>-->

              <div
                v-show="!showImage"
                class="placeholder-img w-full object-fill rounded-t-lg"
              ></div>
              <img
                v-show="showImage"
                class="h-full w-full object-fill rounded-t-lg"
                :src="`${item.img}`"
                :alt="item.title"
                @load="imageLoaded"
              />
            </div>

            <div>
              <div class="flex flex-col justify-between p-4 pb-10 h-60">
                <div>
                  <h2 class="text-2xl font-bold">
                    {{ item.title }}
                  </h2>
                  <p class="my-2 leading-normal">
                    {{ item.text }}
                  </p>
                </div>
                <div>
                  <a
                    class="button w-36 bg-yellow-500 text-white px-3 py-2 rounded-md shadow"
                    href="https://wa.me/2348141588051?text=Hello, I need more information about this service"
                    >Learn more <i class="fab fa-whatsapp"></i
                  ></a>
                  <!--                  <router-link
                    :to="{ name: item.href }"
                    class="button w-36 bg-yellow-500 text-white px-3 py-2 rounded-md shadow"
                    >Learn more</router-link
                  >-->
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
// Import Swiper styles
import "swiper/css";
import "swiper/css/pagination";

// import Swiper core and required modules
import SwiperCore, { Navigation, Pagination } from "swiper";
import { ref } from "vue";
import ImageLoadingComponent from "./ImageLoadingComponent.vue";

SwiperCore.use([Pagination]);
// install Swiper modules
SwiperCore.use([Navigation]);

const imageUrl = ref<any>("/hero/register.jpg");
const services = ref<any>([
  {
    img: "/hero/passport.jpg",
    title: "Visa Arrangement",
    text: "Guaranteed Student visas and Travel visa to over 30 countries, We make prepare you for visa interviews to get you qualified. ",
    href: "Service",
  },
  {
    img: "/hero/plane.jpg",
    title: "Flight Booking",
    text: "get more information here",
    href: "Service",
  },
  {
    img: "/hero/accomodation.jpg",
    title: "Hotel Booking",
    text: "get more information here",
    href: "Service",
  },
]);

const showImage = ref(false);

function imageLoaded() {
  showImage.value = true;
}
</script>

<style lang="scss" scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
.swiper {
  width: 100%;
  height: 100%;
}

.swiper-slide {
  text-align: center;
  font-size: 18px;
  background: #fff;

  /* Center slide text vertically */
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  -webkit-justify-content: center;
  justify-content: center;
  -webkit-box-align: center;
  -ms-flex-align: center;
  -webkit-align-items: center;
  align-items: center;
}

.swiper-slide img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.image-responsive {
  @apply w-60 h-60;
}
@keyframes placeHolderShimmer {
  0% {
    background-position: -468px 0;
  }
  100% {
    background-position: 468px 0;
  }
}

.placeholder-img {
  animation-duration: 1.25s;
  animation-fill-mode: forwards;
  animation-iteration-count: infinite;
  animation-name: placeHolderShimmer;
  animation-timing-function: linear;
  background: darkgray;
  background: linear-gradient(to right, #e0ded1 10%, #fefbeb 18%, #e0ded1 33%);
  background-size: 800px 104px;

  padding: 0.5em 0.5em 0.5em;
}
</style>
