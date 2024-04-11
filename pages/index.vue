<template>
  <UiContainer class="flex min-h-dvh items-center justify-center text-white">
    <div class="w-full max-w-[340px]">
      <div class="text-center">
        <h1 class="text-3xl font-semibold lg:text-4xl">Sign In</h1>
        <p class="mt-2 text-lg text-muted-foreground">
          Welcome back! Enter your details to get started!
        </p>
      </div>

      <form @submit="submit" class="mt-10">
        <fieldset :disabled="isSubmitting" class="grid gap-5">
          <UiVeeInput name="email" label="Email *" placeholder="johndoe@example.com" type="email" />
          <UiVeeInput name="password" label="Password *" type="password" />

          <p>
            <NuxtLink
              class="text-sm font-semibold text-primary hover:underline"
              to="/forgort-password"
              >Forgot Password?</NuxtLink
            >
          </p>

          <UiButton type="submit" class="w-full"> Sign In </UiButton>

          <UiDivider label="or" />

          <UiButton @click="signInWithGoogle" type="button" class="w-full" variant="outline">
            <Icon name="logos:google-icon" /> Sign In with Google
          </UiButton>
        </fieldset>
      </form>
    </div>
  </UiContainer>
</template>

<script lang="ts">
  import { GoogleAuthProvider } from "firebase/auth";

  export const googleAuthProvider = new GoogleAuthProvider();
</script>

<script lang="ts" setup>
  import { signInWithEmailAndPassword, signInWithPopup, updateProfile } from "firebase/auth";
  import { toast } from "vue-sonner";

  // get auth instance
  const auth = useFirebaseAuth();

  const { handleSubmit, isSubmitting } = useForm({
    validationSchema: toTypedSchema(LoginSchema),
  });

  const submit = handleSubmit(async (values, ctx) => {
    const loading = toast("Loading...", {
      description: "We are signin you in!",
    });

    try {
      await signInWithEmailAndPassword(auth!, values.email, values.password);
      toast.success("Sign In successfully", {
        id: loading,
      });

      // redirect to the dashboard
      return await navigateTo("/admin/dashboard", { replace: true });
    } catch (error: any) {
      //show error
      console.log(error.message);

      toast.error(error.message, {
        id: loading,
      });
    }
  });

  const signInWithGoogle = async () => {
    const loading = toast("Loading...", {
      description: "We are signin you in!",
    });

    try {
      await signInWithPopup(auth!, googleAuthProvider);

      toast.success("Sign In successfully", {
        id: loading,
      });

      // redirect to the dashboard
      return await navigateTo("/admin/dashboard", { replace: true });
    } catch (error: any) {
      //show error
      console.log(error.message);

      toast.error(error.message, {
        id: loading,
      });
    }
  };
</script>

<style></style>
