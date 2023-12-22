<template>
  <section class="flex items-center justify-between mb-10">
    <h1 class="text-4xl font-extrabold">Summary</h1>
    <div>
      <USelectMenu v-model="selectedView" :options="transactionViewOptions" />
    </div>
  </section>
  <section
    class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 sm:gap-16 mb-10"
  >
    <Trend
      color="green"
      title="Income"
      :amount="4000"
      :last-amount="3000"
      :loading="false"
    />
    <Trend
      color="red"
      title="Income"
      :amount="400"
      :last-amount="100"
      :loading="false"
    />
    <Trend
      color="green"
      title="Income"
      :amount="400"
      :last-amount="333"
      :loading="false"
    />
    <Trend
      color="red"
      title="Income"
      :amount="400"
      :last-amount="122"
      :loading="false"
    />
  </section>

  <section>
    <div
      v-for="(transactionsOnDay, date) in transactionsGroupedByDate"
      :key="date"
      class="mb-10"
    >
      <DailyTransactionSummary :date="date" :transactions="transactionsOnDay" />
      <Transaction
        v-for="transaction in transactionsOnDay"
        :key="transaction.id"
        :transaction="transaction"
      />
    </div>
  </section>
</template>
<script setup>
import { transactionViewOptions } from "~/constants";

const selectedView = ref(transactionViewOptions[1]);

const supabase = useSupabaseClient();
const { data: transactions, pending } = await useAsyncData(
  "transactions",
  async () => {
    const { data, error } = await supabase.from("transactions").select();

    if (error) return [];
    return data;
  }
);

const transactionsGroupedByDate = computed(() => {
  let grouped = {};
  for (const transaction of transactions.value) {
    const date = new Date(transaction.created_at).toISOString().split("T")[0];
    if (!grouped[date]) {
      grouped[date] = [];
    }
    grouped[date].push(transaction);
  }
  return grouped;
});
</script>
