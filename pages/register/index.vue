<template>
  <UiContainer class="flex min-h-dvh items-center justify-center text-white">
    <div class="w-full max-w-[340px]">
      <div class="text-center">
        <h1 class="text-3xl font-semibold lg:text-4xl">Create account</h1>
        <p class="mt-2 text-lg text-muted-foreground">Start managing your pets today!</p>
      </div>

      <form @submit="submit" class="mt-10">
        <fieldset :disabled="isSubmitting" class="grid gap-5">
          <UiVeeInput name="name" label="Name *" placeholder="John Doe" />
          <UiVeeInput name="email" label="Email *" placeholder="johndoe@example.com" type="email" />
          <UiVeeInput name="password" label="Password *" type="password" />

          <UiButton type="submit" class="w-full"> Create Account </UiButton>

          <UiDivider label="or" />

          <UiButton type="button" class="w-full" variant="outline">
            <Icon name="logos:google-icon" /> Sign up with Google
          </UiButton>
        </fieldset>
      </form>
    </div>
  </UiContainer>
</template>

<script lang="ts" setup>
  import { createUserWithEmailAndPassword, updateProfile } from "firebase/auth";
  import { toast } from "vue-sonner";

  // get auth instance
  const auth = useFirebaseAuth();

  const { handleSubmit, isSubmitting } = useForm({
    validationSchema: toTypedSchema(RegisterSchema),
  });

  const submit = handleSubmit(async (values, ctx) => {
    const loading = toast("Loading...", {
      description: "Creating your account",
    });

    try {
      const { user } = await createUserWithEmailAndPassword(auth!, values.email, values.password);
      await updateProfile(user, { displayName: values.name });

      toast.success("Account created successfully", {
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
</script>

<style></style>
