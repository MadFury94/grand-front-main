<template>
  <div id="appU" class="bg-yellow-50">
    <TransitionRoot as="template" :show="appState.sidebarOpen">
      <Dialog
        as="div"
        class="fixed inset-0 flex z-40 md:hidden"
        @close="appState.sidebarOpen = false"
      >
        <TransitionChild
          as="template"
          enter="transition-opacity ease-linear duration-300"
          enter-from="opacity-0"
          enter-to="opacity-100"
          leave="transition-opacity ease-linear duration-300"
          leave-from="opacity-100"
          leave-to="opacity-0"
        >
          <DialogOverlay class="fixed inset-0 bg-gray-600 bg-opacity-75" />
        </TransitionChild>
        <TransitionChild
          as="template"
          enter="transition ease-in-out duration-300 transform"
          enter-from="-translate-x-full"
          enter-to="translate-x-0"
          leave="transition ease-in-out duration-300 transform"
          leave-from="translate-x-0"
          leave-to="-translate-x-full"
        >
          <div
            class="relative flex-1 flex flex-col max-w-xs w-full pt-5 pb-4 bg-white"
          >
            <TransitionChild
              as="template"
              enter="ease-in-out duration-300"
              enter-from="opacity-0"
              enter-to="opacity-100"
              leave="ease-in-out duration-300"
              leave-from="opacity-100"
              leave-to="opacity-0"
            >
              <div class="absolute top-0 right-0 -mr-12 pt-2">
                <button
                  type="button"
                  class="ml-1 flex items-center justify-center h-10 w-10 rounded-full focus:outline-none focus:ring-2 focus:ring-inset focus:ring-white"
                  @click="appState.sidebarOpen = false"
                >
                  <span class="sr-only">Close sidebar</span>
                  <XIcon class="h-6 w-6 text-white" aria-hidden="true" />
                </button>
              </div>
            </TransitionChild>
            <div class="flex-shrink-0 flex items-center px-4">
              <CompanyLogo :app-state="appState" :text-color="'text-black'" />
            </div>
            <div class="mt-5 flex-1 h-0 overflow-y-auto">
              <nav class="px-2 space-y-1">
                <router-link
                  v-for="item in navigation"
                  :key="item.name"
                  :to="{ name: item.href }"
                  :class="[
                    route.name === item.href
                      ? 'bg-gray-100 text-gray-900'
                      : 'text-gray-600 hover:bg-gray-50 hover:text-gray-900',
                    'group flex items-center px-2 py-2 text-base  rounded-md',
                  ]"
                >
                  <component
                    :is="null"
                    :class="[
                      route.name === item.href
                        ? 'text-gray-500'
                        : 'text-gray-400 group-hover:text-gray-500',
                      'mr-4 flex-shrink-0 h-6 w-6',
                    ]"
                    aria-hidden="true"
                  />
                  <i class="pr-3" :class="item.icon"></i>

                  {{ item.name }}
                </router-link>
              </nav>
            </div>
            <nav
              class="flex justify-center bg-gray-800 py-2 -mb-4"
              v-if="
                appState.user.role === 'admin' || appState.user.role === 'staff'
              "
            >
              <router-link :to="{ name: 'AdminDashboard' }"
                ><span class="text-center text-red-500 py-2"
                  >Manager</span
                ></router-link
              >
            </nav>
          </div>
        </TransitionChild>

        <div class="flex-shrink-0 w-14" aria-hidden="true">
          <!-- Dummy element to force sidebar to shrink to fit close icon -->
        </div>
      </Dialog>
    </TransitionRoot>

    <!-- Static sidebar for desktop -->
    <UserSideBar
      :app-state="appState"
      :navigation="navigation"
      :route="route"
    />

    <!-- Static sidebar for desktop -->

    <div class="md:pl-64 flex flex-col flex-1">
      <div class="sticky top-0 z-10 flex-shrink-0 flex h-16 shadow bg-white">
        <button
          type="button"
          class="px-4 border-r border-gray-200 text-gray-500 focus:outline-none focus:ring-2 focus:ring-inset focus:ring-indigo-500 md:hidden"
          @click="appState.sidebarOpen = true"
        >
          <span class="sr-only">Open sidebar</span>
          <i class="fad fa-bars"></i>
        </button>
        <div class="flex-1 px-4 flex justify-between">
          <div class="flex-1 flex">
            <!--            serach bar area-->
          </div>
          <div class="ml-4 flex items-center md:ml-6">
            <button
              type="button"
              class="bg-white p-1 rounded-full text-gray-400 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
            >
              <span class="sr-only">View notifications</span>
              <BellIcon class="h-6 w-6" aria-hidden="true" />
            </button>

            <!-- Profile dropdown -->
            <Menu as="div" class="ml-3 relative">
              <div>
                <MenuButton
                  class="max-w-xs bg-white flex items-center text-sm rounded-full focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
                >
                  <span class="sr-only">Open user menu</span>
                  <img
                    class="h-8 w-8 rounded-full"
                    src="https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80"
                    alt=""
                  />
                </MenuButton>
              </div>
              <transition
                enter-active-class="transition ease-out duration-100"
                enter-from-class="transform opacity-0 scale-95"
                enter-to-class="transform opacity-100 scale-100"
                leave-active-class="transition ease-in duration-75"
                leave-from-class="transform opacity-100 scale-100"
                leave-to-class="transform opacity-0 scale-95"
              >
                <MenuItems
                  class="origin-top-right absolute right-0 mt-2 w-48 rounded-md shadow-lg py-1 bg-white ring-1 ring-black ring-opacity-5 focus:outline-none"
                >
                  <MenuItem
                    v-for="item in userNavigation"
                    :key="item.name"
                    v-slot="{ active }"
                  >
                    <a
                      :href="item.href"
                      :class="[
                        active ? 'bg-gray-100' : '',
                        'block px-4 py-2 text-sm text-gray-700',
                      ]"
                      >{{ item.name }}</a
                    >
                  </MenuItem>
                  <div
                    @click="logOutUser"
                    class="cursor-pointer flex items-center block px-4 py-2 text-sm text-gray-700"
                  >
                    <i class="fad fa-power-off text-lg text-red-500 pr-2"></i>
                    <p>Logout</p>
                  </div>
                </MenuItems>
              </transition>
            </Menu>
          </div>
        </div>
      </div>
      <h1 class="text-xl py-4 text-gray-500 container mx-auto px-2 md:px-10">
        {{ $route.meta.title }}
      </h1>

      <router-view
        v-slot="{ Component, route }"
        class="h-screen my-auto container mx-auto px-2 md:px-10 pt-4"
      >
        <transition name="route" mode="out-in">
          <component :is="Component" :key="route.path" />
        </transition>
      </router-view>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import {
  Dialog,
  DialogOverlay,
  Menu,
  MenuButton,
  MenuItem,
  MenuItems,
  TransitionChild,
  TransitionRoot,
} from "@headlessui/vue";
import { BellIcon, XIcon } from "@heroicons/vue/outline";
import { appState, logOutUser } from "../store/store";
import CompanyLogo from "../CompanyLogo.vue";
import { useRoute } from "vue-router";
import UserSideBar from "./UserSideBar.vue";
import LogOutComponent from "../components/LogOutComponent.vue";

const navigation = [
  {
    name: "Dashboard",
    href: "UserDashboard",
    icon: "fad fa-sitemap",
  },
  {
    name: "Profiles",
    href: "AllProfiles",
    icon: "far fa-user-circle",
  },

  // {
  //   name: "Documents",
  //   href: "DocumentsUpload",
  //   icon: "fad fa-folders",
  // },

  {
    name: "My Wish List",
    href: "MyDestinations",
    icon: "fad fa-globe-asia",
  },
];
const userNavigation = [
  { name: "Your Profile", href: "#" },
  { name: "Settings", href: "#" },
];

export default {
  components: {
    LogOutComponent,
    UserSideBar,
    CompanyLogo,
    Dialog,
    DialogOverlay,
    Menu,
    MenuButton,
    MenuItem,
    MenuItems,
    TransitionChild,
    TransitionRoot,
    BellIcon,
    XIcon,
  },
  setup() {
    const route = useRoute();
    const sidebarOpen = ref(false);

    return {
      navigation,
      userNavigation,
      sidebarOpen,
      appState,
      route,
      logOutUser,
    };
  },
};
</script>

<style>
#appU {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  @apply bg-yellow-50;
}
.route-enter-from {
  opacity: 0;
  transform: translateY(100px);
}
.route-enter-active {
  transition: all 0.3s ease-out;
}
.route-leave-to {
  opacity: 0;
  transform: translateY(-100px);
}

.route-leave-active {
  transition: all 0.3s ease-in;
}
</style>
