<template>
  <div>
    <h1 class="text-2xl font-semibold lg:text-3xl">Welcome back {{ user?.displayName }}</h1>
  </div>
</template>

<script lang="ts" setup>
  definePageMeta({
    middleware: "auth",
  });

  const user = useCurrentUser();
</script>

<style></style>
