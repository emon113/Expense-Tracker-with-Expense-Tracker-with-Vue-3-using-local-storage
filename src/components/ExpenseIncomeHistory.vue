<template>

    <div class="history">
        <h2>History</h2>
        <button class="clear-all-btn" @click="clearAll()">Clear All</button>
    </div>
      <ul id="list" class="list">
        <li 
            v-for="transaction in transactionsHistory" :key="transaction.id" 
            :class="transaction.amount < 0 ? 'minus' : 'plus'"
        >
          {{ transaction.text }} <span>${{ transaction.amount }}</span>
          <button @click="deleteTransaction(transaction.id)" class="delete-btn">x</button>
        </li>
      </ul>
</template>

<script setup>
import { defineProps } from 'vue';

const emit = defineEmits(['transactionDeleted', 'clearAll']);

const props = defineProps({
    transactionsHistory: {
        type: Array,
        required: true
    }
})

const deleteTransaction = (id) => {
    emit('transactionDeleted', id);
}

const clearAll = () => {
    emit('clearAll');
}

</script>

<style scoped>
.history {
    display: flex;
    justify-content: space-between;
    align-items: center;

    border-bottom: 1px solid #ccc;
  }
</style>