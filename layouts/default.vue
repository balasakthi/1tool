<template>
  <v-app v-if="userStore.user">
    <AppBar />
    <v-container fluid>
      <v-main>
        <slot />
      </v-main>
    </v-container>
  </v-app>
</template>

<script setup>
import { useUserStore } from "~/stores/user";

const userStore = useUserStore();
</script>
