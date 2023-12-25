<template>
  <header class="flex justify-between items-center mt-10">
    <NuxtLink to="/" class="text-xl font-bold">Finance Tracker</NuxtLink>
    <div>
      <UDropdown
        :items="items"
        :ui="{ item: { disabled: 'cursor-text select-text' }, width: 'w-64' }"
        v-if="user"
      >
        <UAvatar :src="url" alt="Avatar" />

        <UButton @click="signout" class="ml-4">Sign out</UButton>
        <UButton @click="navigateTo('/settings')" class="ml-4"
          >Settings</UButton
        >
      </UDropdown>
    </div>
  </header>
</template>
<script setup>
const supabase = useSupabaseClient();
const user = useSupabaseUser();
const { url } = useAvatarUrl();

const signout = async () => {
  await supabase.auth.signOut();
  return navigateTo("/login");
};
</script>
