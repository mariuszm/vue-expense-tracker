<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { Toaster, toast } from 'vue-sonner'
import AppHeader from './components/AppHeader.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'

const transactions = ref([])

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'))
  if (savedTransactions) {
    transactions.value = savedTransactions
  }
})

// get total
const total = computed(() =>
  transactions.value.reduce((acc, transaction) => acc + transaction.amount, 0).toFixed(2)
)

// get income
const income = computed(() =>
  transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2)
)

// get expenses
const expenses = computed(() =>
  transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2)
)

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  })

  saveTransactionToLocalStorage()
  toast.success('Transaction added')
}

const generateUniqueId = () => Math.floor(Math.random() * 1000000)

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
  saveTransactionToLocalStorage()
  toast.success('Transaction deleted')
}

const saveTransactionToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}
</script>

<template>
  <Toaster richColors />
  <AppHeader />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>
