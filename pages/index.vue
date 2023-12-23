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
      :loading="isLoading"
    />
    <Trend
      color="red"
      title="Income"
      :amount="400"
      :last-amount="100"
      :loading="isLoading"
    />
    <Trend
      color="green"
      title="Income"
      :amount="400"
      :last-amount="333"
      :loading="isLoading"
    />
    <Trend
      color="red"
      title="Income"
      :amount="400"
      :last-amount="122"
      :loading="isLoading"
    />
  </section>

  <section class="flex justify-between mb-10">
    <div>
      <h2 class="text-2xl font-extrabold">Transactions</h2>
      <div class="text-gray-500 dark:text-gray-400">
        Income : {{ income.length }}, Expense {{ expense.length }}
      </div>
    </div>
    <div>
      <TransactionModal v-model="isOpen" />
      <UButton
        icon="i-heroicons-plus-circle"
        color="white"
        variant="solid"
        label="Add"
        @click="isOpen = true"
      />
    </div>
  </section>

  <section v-if="!isLoading">
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
        @deleted="refreshTransactions"
      />
    </div>
  </section>
  <section v-else>
    <USkeleton class="h-8 w-full mb-2" />
  </section>
</template>
<script setup>
import { transactionViewOptions } from "~/constants";

const selectedView = ref(transactionViewOptions[1]);
const isLoading = ref(false);
const isOpen = ref(false);
const transactions = ref([]);

const income = computed(() =>
  transactions.value.filter((t) => t.type === "Income")
);
const expense = computed(() =>
  transactions.value.filter((t) => t.type === "Expense")
);
const incomeTotal = computed(() =>
  income.value.reduce((acc, cur) => acc + cur, 0)
);
const expenseTotal = computed(() =>
  expense.value.reduce((acc, cur) => acc + cur, 0)
);

const supabase = useSupabaseClient();

const fetchTransactions = async () => {
  try {
    isLoading.value = true;
    const { data } = await useAsyncData("transactions", async () => {
      const { data, error } = await supabase.from("transactions").select();
      if (error) return [];
      return data;
    });

    return data.value;
  } catch (error) {
  } finally {
    isLoading.value = false;
  }
};

const refreshTransactions = async () =>
  (transactions.value = await fetchTransactions());
await refreshTransactions();

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
