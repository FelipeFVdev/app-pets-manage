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

    <UiContainer class="py-10">
      <div class="flex flex-col justify-between gap-5 lg:flex-row">
        <h1 class="text-2xl font-semibold lg:text-3xl">Welcome back {{ user?.displayName }}</h1>
        <UiButton @click="dialog = true">Add pet</UiButton>
      </div>

      <section class="mt-10 grid grid-cols-1 gap-5 md:grid-cols-2 lg:grid-cols-3">
        <template v-for="p in pets" :key="p.id">
          <UiCard>
            <UiCardContent>
              <p class="font-semibold">
                <span class="mr-2 text-muted-foreground">Name: </span>{{ p.name }}
              </p>
              <p class="text-muted-foreground"><span class="mr-2">Age: </span>{{ p.age }}</p>
            </UiCardContent>
            <UiCardFooter class="gap-3">
              <UiButton size="sm" @click="setEdit(p)">Edit</UiButton>
              <UiButton size="sm" @click="remove(p)" variant="ghost">Delete</UiButton>
            </UiCardFooter>
          </UiCard>
        </template>
      </section>
    </UiContainer>

    <PetModal v-model="dialog" :pet="editItem" />
  </div>
</template>

<script lang="ts" setup>
  import { signOut } from "firebase/auth";
  import { collection, deleteDoc, doc, getDocs, query, where } from "firebase/firestore";
  import { toast } from "vue-sonner";
  import type { Pet } from "~/type";

  definePageMeta({
    middleware: "auth",
  });

  const db = useFirestore();

  const user = useCurrentUser();

  const dialog = ref(false);

  const editItem = ref<Pet>();

  const auth = useFirebaseAuth();

  const collectionName = "pets";

  const q = query(collection(db, collectionName), where("userId", "==", user.value?.uid));

  const { data: pets, pending } = useCollection<Pet>(q);

  const loggout = async () => {
    await signOut(auth!);
    await navigateTo("/", { replace: true });
  };

  const remove = async (pet: Pet) => {
    const res = confirm("Are you sure you want to remove this pet?");
    if (res) {
      await deleteDoc(doc(db, collectionName, pet.id!));
      toast.success("Pet removed successfully");
    }
  };

  const setEdit = (pet: Pet) => {
    editItem.value = pet;
    dialog.value = true;
  };

  watch(dialog, (v) => {
    if (!v) {
      editItem.value = undefined;
    }
  });
</script>

<style></style>
