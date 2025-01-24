<template>
  <div class="p-4 max-w-4xl mx-auto">
    <div class="flex items-center justify-between">
      <h1 class="text-2xl font-bold mb-4">Sorting Training System</h1>
      <button
        v-if="!sortingActive"
        class="bg-[#FF8D00] text-white px-4 py-2 rounded hover:bg-opacity-80"
        @click="openModal"
      >
        Start Sorting!
      </button>
      <button
        v-else
        class="bg-red-500 text-white px-4 py-2 rounded hover:bg-opacity-80"
        @click="stopSorting"
      >
        Stop Sorting!
      </button>
    </div>

    <Modal v-if="modalOpen" :on-close="closeModal" :on-start="startSorting" />

    <div v-if="people.length > 0">
      <div class="flex space-x-4">
        <button
          class="bg-blue-500 text-white px-4 py-2 rounded"
          @click="checkOrder"
        >
          Check Order
        </button>
        <button
          class="bg-green-500 text-white px-4 py-2 rounded"
          @click="autoSort"
        >
          Auto Sort
        </button>
      </div>

      <div class="flex items-center justify-between p-4 bg-neutral-50 mt-4">
        <span class="text-gray-700">Time: {{ timer }} seconds</span>
        <span>{{ people.length }} people in the list</span>
      </div>

      <!-- <table class="w-full bg-white rounded-lg overflow-hidden shadow">
        <thead>
          <tr class="bg-gray-50 border-b">
            <th class="text-left px-6 py-3 text-sm font-medium text-gray-500">
              Email
            </th>
            <th class="text-left px-6 py-3 text-sm font-medium text-gray-500">
              Potatoes
            </th>
            <th class="text-left px-6 py-3 text-sm font-medium text-gray-500">
              Name
            </th>
          </tr>
        </thead>
        <VueDraggable v-model="people" tag="tbody">
          <tr
            v-for="person in people"
            :key="person.email"
            class="border-b last:border-b-0 hover:bg-gray-50 transition-colors duration-200"
          >
            <td class="px-6 py-4 text-sm text-gray-900">{{ person.email }}</td>
            <td class="px-6 py-4 text-sm text-gray-900">
              {{ person.potatoes }}
            </td>
            <td class="px-6 py-4 text-sm text-gray-900">{{ person.name }}</td>
          </tr>
        </VueDraggable>
      </table> -->

      <table class="w-full bg-white rounded-lg overflow-hidden shadow">
        <thead>
          <tr class="bg-gray-50 border-b">
            <th class="text-left px-6 py-3 text-sm font-medium text-gray-500">
              Email
            </th>
            <th class="text-left px-6 py-3 text-sm font-medium text-gray-500">
              Potatoes
            </th>
            <th class="text-left px-6 py-3 text-sm font-medium text-gray-500">
              Name
            </th>
          </tr>
        </thead>
        <VueDraggable v-model="people" tag="tbody">
          <tr
            v-for="person in people"
            :key="person.email"
            class="border-b last:border-b-0 hover:bg-gray-50 transition-colors duration-200"
          >
            <td class="px-6 py-4 text-sm text-gray-900">{{ person.email }}</td>
            <td class="px-6 py-4 text-sm text-gray-900">
              {{ person.potatoes }}
            </td>
            <td class="px-6 py-4 text-sm text-gray-900">{{ person.name }}</td>
          </tr>
        </VueDraggable>
      </table>
    </div>
  </div>
</template>

<script>
import { defineComponent, ref, onUnmounted } from "vue";

import Modal from "./components/Modal.vue";
import { VueDraggable } from "vue-draggable-plus";

export default defineComponent({
  name: "App",

  components: {
    Modal,
    VueDraggable,
  },

  setup() {
    const modalOpen = ref(false);
    const people = ref([]);
    const timer = ref(0);
    const sortingActive = ref(false);
    const intervalId = ref(null);

    const generateRandomData = (count) => {
      const data = [];
      const usedPotatoes = new Set();
      for (let i = 0; i < count; i++) {
        let potatoes;
        do {
          potatoes = Math.floor(Math.random() * 100) + 1;
        } while (usedPotatoes.has(potatoes));
        usedPotatoes.add(potatoes);
        data.push({
          email: `user${i}@example.com`,
          potatoes,
          name: `User ${i}`,
        });
      }
      return data;
    };

    const openModal = () => (modalOpen.value = true);
    const closeModal = () => (modalOpen.value = false);

    const startSorting = (count) => {
      people.value = generateRandomData(count);
      // console.log("people.value: >>>>>>>>>", people.value);
      modalOpen.value = false;
      timer.value = 0;
      sortingActive.value = true;
      intervalId.value = setInterval(() => (timer.value += 1), 1000);
    };

    const stopSorting = () => {
      clearInterval(intervalId.value);
      intervalId.value = null;
      timer.value = 0;
      sortingActive.value = false;
      people.value = [];
    };

    const autoSort = () => {
      people.value.sort((a, b) => b.potatoes - a.potatoes);
      clearInterval(intervalId.value);

      const elapsedSeconds = timer.value; // Save the current timer value
      timer.value = 0;
      sortingActive.value = false;

      alert(`Success! You sorted the list in ${elapsedSeconds} seconds.`);
      people.value = [];
    };

    const checkOrder = () => {
      const isSorted = people.value.every((person, i, arr) => {
        return i === 0 || arr[i - 1].potatoes >= person.potatoes;
      });
      if (isSorted) {
        clearInterval(intervalId.value);
        timer.value = 0;
        sortingActive.value = false;
        alert(`Success! You sorted the list in ${timer.value} seconds.`);
      } else {
        alert("The list is not sorted correctly. Keep trying");
      }
    };

    onUnmounted(() => {
      clearInterval(intervalId.value);
    });

    return {
      modalOpen,
      people,
      timer,
      sortingActive,
      openModal,
      closeModal,
      startSorting,
      stopSorting,
      autoSort,
      checkOrder,
    };
  },
});
</script>

<style scoped></style>
