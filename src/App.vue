<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import ExpenseIncome from './components/ExpenseIncome.vue';
import ExpenseIncomeHistory from './components/ExpenseIncomeHistory.vue';
import AddExpenseIncome from './components/AddExpenseIncome.vue';
import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';

// this array will hold the transactions history
const transactionsHistory = ref([]);

// this varible will be used to invoke the vue-toastification to show success and error toast
const toast = useToast();

// this will initialize the app by fetching data from local storage
onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactionsHistory.value = savedTransactions;
  }
});

// this function will compute the total available balance
const total =  computed(()=> {
  return transactionsHistory.value.reduce((acc, transaction) => {
      return acc + transaction.amount;
  }, 0);
});

// this function will compute the total income
const income =  computed(()=> {
  return transactionsHistory.value.filter((transaction) => transaction.amount > 0).reduce((acc, transaction) => {
      return acc + transaction.amount;
  }, 0);
});

// this function will compute the total expenses
const expenses =  computed(()=> {
  return transactionsHistory.value.filter((transaction) => transaction.amount < 0).reduce((acc, transaction) => {
      return acc + transaction.amount;
  }, 0);
});

// this function will add new transaction
const handleNewTransactionSubmitted = (transactionData) => {
  transactionsHistory.value.push({
    id:generateUniqueID(),
    text: transactionData.text,
    amount: transactionData.amount,
  })

  saveToLocalStorage();
  transactionData.amount > 0 ? toast.success("New Income added!")  : toast.success("New Expense added!");
  console.log(transactionsHistory.value);
}

// this function will generate a unique id for a transaction
const generateUniqueID = () => {
  const maxId = transactionsHistory.value.reduce((max, transaction) => {
    return transaction.id > max ? transaction.id : max;
  }, 0);

  return parseInt(maxId) + 1;
}

// this function will delete a transaction 
const deleteTransaction = (id) => {
  transactionsHistory.value = transactionsHistory.value.filter(transaction => transaction.id !== id);
  saveToLocalStorage();

  toast.success("Transaction deleted!");
}

// this function will delete all the transaction together
const clearAll = () => {
  if(transactionsHistory.value.length === 0) {
    toast.success("No Transactions available!");
    return;
  }
  transactionsHistory.value = [];
  saveToLocalStorage();
  toast.success("All Transaction cleared!");
}

// this function will save the transactionsHistory data to local store
const saveToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactionsHistory.value))
}
</script>

<template>
  <Header />
  <div class="container">
    <Balance :total="total"/>
    <ExpenseIncome :income="income" :expenses="expenses" />
    <ExpenseIncomeHistory 
      :transactionsHistory="transactionsHistory" 
      @transactionDeleted="deleteTransaction"  
      @clearAll="clearAll"
    />
    <AddExpenseIncome @transactionSubmitted="handleNewTransactionSubmitted" />
  </div>
</template>

 <style>

</style>
