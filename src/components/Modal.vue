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
          min="20"
          max="100"
          class="border border-gray-300 rounded p-2 w-full mb-4"
        />
        <div class="flex justify-end space-x-2">
          <button
            type="submit"
            class="bg-blue-500 text-white px-4 py-2 rounded"
          >
            Start
          </button>
          <button
            type="button"
            class="bg-gray-300 px-4 py-2 rounded"
            @click="onClose"
          >
            Close
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "Modal",

  props: ["onClose", "onStart"],

  setup(props) {
    const count = ref(20);

    const handleStart = () => {
      if (count.value >= 20 && count.value <= 100) {
        props.onStart(count.value);
      } else {
        alert("Please enter a number between 20 and 100.");
      }
    };

    return { count, handleStart };
  },
});
</script>
