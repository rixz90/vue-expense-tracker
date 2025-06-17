<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpense :income="+income" :expenses="+expenses" />
    <TransactionList :transactions="transactions" @transaction-deleted="handleDelete" />
    <AddTransaction @add-transaction="handleAddSubmit" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';

const toast = useToast();
const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
  if (savedTransactions) {
    transactions.value = savedTransactions
  }
});

const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => acc + transaction.amount, 0);
})

const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

const handleAddSubmit = (transactionData) => {
  const newTransaction = {
    id: transactions.value.length + 1,
    text: transactionData.text,
    amount: transactionData.amount
  };
  transactions.value.push(newTransaction);
  saveTransactionToLocale();
  toast.success('Transaction added successfully!');
};

const handleDelete = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);
  toast.success('Transaction deleted successfully!');
  saveTransactionToLocale();
};

const saveTransactionToLocale = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}
</script>

<style scoped></style>
