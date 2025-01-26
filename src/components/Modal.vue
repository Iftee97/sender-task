<template>
  <div
    class="fixed inset-0 flex items-center justify-center bg-gray-900 bg-opacity-50"
  >
    <div class="bg-white p-6 rounded shadow-lg">
      <h2 class="text-lg font-bold mb-4">Enter number of people</h2>
      <form action="" @submit.prevent="handleStart">
        <input
          type="number"
          v-model="count"
          :min="MIN_COUNT"
          :max="MAX_COUNT"
          class="border border-gray-300 rounded p-2 w-full mb-4"
        />
        <div class="flex justify-end space-x-2">
          <button
            type="submit"
            class="bg-orange-400 hover:bg-orage-500 text-white px-4 py-1 rounded text-sm"
          >
            Start
          </button>
          <button
            type="button"
            class="bg-red-400 hover:bg-red-500 px-4 py-1 rounded text-sm text-white"
            @click="onClose"
          >
            Close
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const MIN_COUNT = 20; // default: 20
const MAX_COUNT = 100; // default: 100

const props = defineProps(["onClose", "onStart"]);
const count = ref(MIN_COUNT);

const handleStart = () => {
  if (count.value >= MIN_COUNT && count.value <= MAX_COUNT) {
    props.onStart(count.value);
  } else {
    alert(`Please enter a number between ${MIN_COUNT} and ${MAX_COUNT}.`);
  }
};
</script>
