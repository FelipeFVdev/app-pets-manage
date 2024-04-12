<template>
  <div>
    <UiNavbar sticky>
      <UiContainer class="flex h-14 items-center justify-between">
        <NuxtLink class="text-lg font-semibold" to="/admin/dashboard">Pettzz</NuxtLink>

        <div class="flex items-center gap-2">
          <UiButton
            @click="$colorMode.preference = $colorMode.value == 'dark' ? 'light' : 'dark'"
            size="icon-sm"
            variant="ghost"
            tittle="Toggle Theme"
          >
            <span class="sr-only">Toggle Theme</span>
            <Icon name="lucide:sun-medium" class="h-4 w-4" />
          </UiButton>
          <UiButton @click="loggout" size="icon-sm" variant="ghost" tittle="Logout">
            <span class="sr-only">Logout</span>
            <Icon name="lucide:log-out" class="h-4 w-4" />
          </UiButton>
        </div>
      </UiContainer>
    </UiNavbar>

    <h1 class="text-2xl font-semibold lg:text-3xl">Welcome back {{ user?.displayName }}</h1>
  </div>
</template>

<script lang="ts" setup>
  import { signOut } from "firebase/auth";

  definePageMeta({
    middleware: "auth",
  });

  const user = useCurrentUser();

  const auth = useFirebaseAuth();

  const loggout = async () => {
    await signOut(auth!);
    await navigateTo("/", { replace: true });
  };
</script>

<style></style>
