<template>
  <UiContainer class="flex min-h-dvh items-center justify-center text-white">
    <div class="w-full max-w-[340px]">
      <div class="text-center">
        <h1 class="text-3xl font-semibold lg:text-4xl">Forgot Password</h1>
        <p class="mt-2 text-lg text-muted-foreground">Let us help you sign in to your account!</p>
      </div>

      <form @submit="submit" class="mt-10">
        <fieldset :disabled="isSubmitting" class="grid gap-5">
          <UiVeeInput name="email" label="Email *" placeholder="johndoe@example.com" type="email" />

          <UiButton type="submit" class="w-full"> Send Instructions </UiButton>

          <UiDivider label="or" />

          <UiButton to="/" type="button" class="w-full" variant="outline"> Go to Sign In </UiButton>
        </fieldset>
      </form>
    </div>
  </UiContainer>
</template>

<script lang="ts" setup>
  import { sendPasswordResetEmail } from "firebase/auth";
  import { toast } from "vue-sonner";

  definePageMeta({
    middleware: "already-logged-in",
  });

  // get auth instance
  const auth = useFirebaseAuth();

  const { handleSubmit, isSubmitting } = useForm({
    validationSchema: toTypedSchema(ForgotPasswordSchema),
  });

  const submit = handleSubmit(async (values, ctx) => {
    const loading = toast("Loading...", {
      description: "We are sending the instructions to your email!",
    });

    try {
      await sendPasswordResetEmail(auth!, values.email, {
        url: "https://localhost:3000/",
      });
      toast.success("Please check your email", {
        id: loading,
        description: "We have send you instructions to reset your password!",
      });

      // redirect to the sign in page
      return await navigateTo("/", { replace: true });
    } catch (error: any) {
      //show error
      console.log(error.message);

      toast.error(error.message, {
        id: loading,
      });
    }
  });
</script>

<style></style>
