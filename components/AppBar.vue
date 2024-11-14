<template>
  <v-navigation-drawer v-model="drawer">
    <v-img class="py-5 ml-3" src="/logo.png" width="80"></v-img>
    <v-divider></v-divider>

    <v-list>
      <v-list-item
        v-for="(item, i) in drawerItems"
        :key="i"
        :to="item.route"
        rounded="xl"
      >
        <template v-slot:prepend>
          <v-icon :icon="item.icon"></v-icon>
        </template>

        <v-list-item-title v-text="item.title"></v-list-item-title>
      </v-list-item>
      <v-divider> </v-divider>
      <v-list-item @click="userStore.logout()" prepend-icon="mdi-logout"
        ><v-list-item-title>Signout</v-list-item-title></v-list-item
      >
    </v-list>
  </v-navigation-drawer>
  <v-app-bar class="border" flat>
    <template v-slot:prepend>
      <v-app-bar-nav-icon
        :icon="drawer ? 'mdi-menu-open' : 'mdi-menu-close'"
        @click="drawer = !drawer"
      ></v-app-bar-nav-icon>
    </template>

    <v-app-bar-title class="text-capitalize">
      {{ displayRouteName }}
    </v-app-bar-title>

    <v-btn variant="text" icon="mdi-magnify" flat></v-btn>

    <v-menu>
      <template v-slot:activator="{ props }">
        <v-btn icon v-bind="props">
          <v-icon>mdi-bell-outline</v-icon>
        </v-btn>
      </template>
      <v-list><v-list-item>No Notifications</v-list-item></v-list>
    </v-menu>

    <v-btn variant="text" icon="mdi-message-text-outline" flat></v-btn>

    <template v-slot:append>
      <v-menu>
        <template v-slot:activator="{ props }">
          <v-avatar class="cursor-pointer" v-bind="props">
            <v-img
              :alt="userStore.user.data.name"
              src="https://avatars0.githubusercontent.com/u/9064066?v=4&s=460"
            ></v-img>
          </v-avatar>
        </template>

        <v-card min-width="300">
          <v-card-text>
            <div class="mx-auto text-center">
              <v-avatar
                image="https://avatars0.githubusercontent.com/u/9064066?v=4&s=460"
                size="80"
              ></v-avatar>

              <h3 class="mt-3">
                Hi,
                <span class="text-capitalize"
                  >{{ userStore.user.data.name }}!</span
                >
              </h3>
              <p class="text-caption mt-1">
                {{ userStore.user.data.mail }}
              </p>
              <v-divider class="my-3"></v-divider>
              <v-row
                ><v-col cols="6">
                  <v-btn
                    variant="text"
                    rounded
                    class="text-capitalize"
                    prepend-icon="mdi-account-outline"
                    to="/profile"
                  >
                    My Profile
                  </v-btn></v-col
                ><v-col cols="6">
                  <v-btn
                    class="text-capitalize"
                    color="red"
                    prepend-icon="mdi-logout"
                    variant="text"
                    rounded
                    @click="userStore.logout()"
                  >
                    Signout
                  </v-btn></v-col
                ></v-row
              >
            </div>
          </v-card-text>
        </v-card>
      </v-menu>
    </template>
  </v-app-bar>
</template>

<script setup>
import { ref } from "vue";
import { useUserStore } from "~/stores/user";

const drawer = ref(null);
const userStore = useUserStore();
const route = useRoute();

const displayRouteName = computed(() => {
  if (route.path === "/") return "Dashboard";
  else {
    const segments = route.path.split("/").filter(Boolean);
    return segments.includes("events")
      ? "Events"
      : segments[0].charAt(0).toUpperCase() + segments[0].slice(1);
  }
});

const drawerItems = ref([
  {
    title: "Dashboard",
    route: "/",
    icon: "mdi-view-dashboard-outline",
  },

  {
    title: "Seminars",
    route: "/seminars",
    icon: "mdi-google-circles-extended",
  },
  {
    title: "Profile",
    route: "/profile",
    icon: "mdi-account-badge",
  },
]);
</script>
