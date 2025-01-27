<template>
  <div
    class="fixed inset-0 flex items-center justify-center bg-gray-900 bg-opacity-50"
  >
    <div class="bg-white rounded-lg shadow-lg w-[576px] overflow-hidden">
      <div
        class="px-6 py-4 border-b border-gray-200 flex justify-between items-center"
        id="modal-header"
      >
        <h2 class="text-xl font-bold">How many people?</h2>
        <button
          @click="props.onClose"
          class="text-gray-400 hover:text-gray-600"
        >
          ×
        </button>
      </div>

      <div class="px-6 py-6" id="modal-content">
        <form @submit.prevent="handleStart">
          <p class="text-gray-600 mb-4">
            Enter a number of how many people you want to add to the list.
          </p>
          <input
            type="number"
            v-model="count"
            :min="MIN_COUNT"
            :max="MAX_COUNT"
            class="border border-gray-300 rounded-lg p-3 w-full mb-6 text-lg"
          />

          <div class="flex justify-end space-x-3" id="modal-footer">
            <button
              type="button"
              @click="props.onClose"
              class="px-6 py-2 rounded-lg bg-gray-100 hover:bg-gray-200 text-gray-800 font-medium"
            >
              Cancel
            </button>
            <button
              type="submit"
              class="px-6 py-2 rounded-lg bg-[#ff6b2c] hover:bg-[#ff5a15] text-white font-medium"
            >
              Start
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const MIN_COUNT = 20;
const MAX_COUNT = 100;

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
