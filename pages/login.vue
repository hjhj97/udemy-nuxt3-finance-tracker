<template>
  <UCard v-if="!success">
    <template #header>
      <div>Signin</div>
    </template>
    <form @submit.prevent="handleLogin">
      <UFormGroup label="Email" name="email" class="mb-4" required>
        <UInput type="email" placeholder="Email" required v-model="email" />
      </UFormGroup>

      <UButton
        type="submit"
        variant="solid"
        color="black"
        :loading="pending"
        :disabled="pending"
        >Signin</UButton
      >
    </form>
  </UCard>
  <UCard v-else>
    <template #header> Email has been sent </template>

    <div class="text-center">
      <p class="mb-4">{{ email }}</p>
      <p><strong>Important:</strong>qqq</p>
    </div>
  </UCard>
</template>
<script setup>
const success = ref(false);
const email = ref("");
const pending = ref(false);
const { toastSuccess } = useAppToast();
const supabase = useSupabaseClient();

useRedirectAuth();

const handleLogin = async () => {
  pending.value = true;
  try {
    const { error } = await supabase.auth.signInWithOtp({
      email: email.value,
      options: {
        emailRedirectTo: "http://localhost:3000/confirm",
      },
    });
    if (error) {
      toastSuccess({
        title: "Error authenticating",
        description: error.message,
      });
    } else {
      success.value = true;
    }
  } finally {
    pending.value = false;
  }
};
</script>
