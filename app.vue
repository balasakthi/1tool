<template>
  <NuxtRouteAnnouncer />
  <NuxtLoadingIndicator />
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>

<script setup>
useSeoMeta({
  title: "1Tool Freelancer App",
  description: "This is 1Tool Freelancer app, let me tell you all about it.",
});
</script>
