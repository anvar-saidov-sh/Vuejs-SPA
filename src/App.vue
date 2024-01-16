<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpensess from './components/IncomeExpensess.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import {useToast } from 'vue-toastification'


import { computed, ref, onMounted } from 'vue';

const toast = useToast()

const transactions = ref([
    {id: 1, text: 'Flavor', amount: -30.99},
    {id: 2, text: 'Salary', amount: 120.99},
    {id: 3, text: 'Book', amount: -10},
    {id: 4, text: 'Camera', amount: 150},
])

onMounted(()=>{
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'))
  if(savedTransactions)
  {
    transactions.value = savedTransactions;
  }
})
//Save to LocalStorage
const saveTransactionsToLocalStorage = () =>{
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}
// Get total 
const total = computed(() =>{
  return transactions.value.reduce((acc, transaction)=>{
    return acc + transaction.amount;
  }, 0)
})
// Get expenses
const expense = computed(() =>{
  return transactions.value.filter((transaction) => transaction.amount <= 0).reduce((acc, transaction)=>{
      return acc + transaction.amount
  }, 0).toFixed(2)
})
// Get income
const income = computed(() =>{
  return transactions.value.filter((transaction) => transaction.amount > 0).reduce((acc, transaction)=>{
      return acc + transaction.amount
  }, 0).toFixed(2)
})

//Add transaction
const handleTransactionSubmitted = (transactionData) =>{
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  })
  saveTransactionsToLocalStorage();
  toast.success('Transaction added');
}

function generateUniqueId(){
  return Math.floor(Math.random() * 1000000)
}


// Delete transaction
const handleTransactionDeleted = (id) =>{
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
  saveTransactionsToLocalStorage()
  toast.success('Transaction deleted')
}

</script>

<template>
  <Header/>
  <div class="container">
    <Balance :total="+total"/>
    <IncomeExpensess :expense="+expense" :income="+income"/>  
    <TransactionList :transactions="transactions" @transaction-deleted="handleTransactionDeleted"/>  
    <AddTransaction @transaction-submitted="handleTransactionSubmitted"/>
  </div>
</template>

