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
      :amount="incomeTotal"
      :last-amount="prevIncomeTotal"
      :loading="pending"
    />
    <Trend
      color="red"
      title="Expense"
      :amount="expenseTotal"
      :last-amount="prevExpenseTotal"
      :loading="pending"
    />
    <Trend
      color="green"
      title="Investment"
      :amount="400"
      :last-amount="333"
      :loading="pending"
    />
    <Trend
      color="red"
      title="Savings"
      :amount="400"
      :last-amount="122"
      :loading="pending"
    />
  </section>

  <section class="flex justify-between mb-10">
    <div>
      <h2 class="text-2xl font-extrabold">Transactions</h2>
      <div class="text-gray-500 dark:text-gray-400">
        Income : {{ incomeCount }}, Expense {{ expenseCount }}
      </div>
    </div>
    <div>
      <TransactionModal v-model="isOpen" @saved="refresh" />
      <UButton
        icon="i-heroicons-plus-circle"
        color="white"
        variant="solid"
        label="Add"
        @click="isOpen = true"
      />
    </div>
  </section>

  <section v-if="!pending">
    <div v-for="(transactionsOnDay, date) in byDate" :key="date" class="mb-10">
      <DailyTransactionSummary :date="date" :transactions="transactionsOnDay" />
      <Transaction
        v-for="transaction in transactionsOnDay"
        :key="transaction.id"
        :transaction="transaction"
        @deleted="refresh"
      />
    </div>
  </section>
  <section v-else>
    <USkeleton class="h-8 w-full mb-2" />
  </section>
</template>
<script setup>
import { useFetchTransactions } from "~/composables/useFetchTransactions";
import { transactionViewOptions } from "~/constants";

const selectedView = ref(transactionViewOptions[1]);
const isOpen = ref(false);
const { current, previous } = useSelectedTimePeriod(selectedView);

const {
  pending,
  refresh,
  transactions: {
    incomeCount,
    incomeTotal,
    expenseCount,
    expenseTotal,
    grouped: { byDate },
  },
} = useFetchTransactions(current);
await refresh();

const {
  refresh: refreshPrevious,
  transactions: {
    incomeTotal: prevIncomeTotal,
    expenseTotal: prevExpenseTotal,
  },
} = useFetchTransactions(previous);
await refreshPrevious();
</script>
