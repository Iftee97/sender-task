<template>
  <div class="p-4 max-w-4xl mx-auto">
    <div class="flex items-center justify-between">
      <h1 class="text-2xl font-bold mb-4">Range-Based Sorting System</h1>
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

      <div class="bg-white rounded-lg overflow-hidden shadow">
        <div class="p-4 bg-gray-50 border-b">
          <span class="text-sm font-medium text-gray-700">
            Current Range: {{ currentRangeText }}
          </span>
        </div>
        <table class="w-full">
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
          <VueDraggable
            v-model="currentPageItems"
            tag="tbody"
            @change="handlePageUpdate"
          >
            <tr
              v-for="person in currentPageItems"
              :key="person.email"
              class="border-b last:border-b-0 hover:bg-gray-50 transition-colors duration-200"
            >
              <td class="px-6 py-4 text-sm text-gray-900">
                {{ person.email }}
              </td>
              <td class="px-6 py-4 text-sm text-gray-900">
                {{ person.potatoes }}
              </td>
              <td class="px-6 py-4 text-sm text-gray-900">{{ person.name }}</td>
            </tr>
          </VueDraggable>
        </table>

        <!-- Pagination Controls -->
        <div class="flex items-center justify-between p-4 bg-gray-50 border-t">
          <div class="flex items-center space-x-2">
            <span class="text-sm text-gray-700">
              Page {{ currentPage }} of {{ totalPages }}
            </span>
          </div>
          <div class="flex space-x-2">
            <button
              @click="prevPage"
              :disabled="currentPage === 1"
              class="px-3 py-1 rounded text-sm"
              :class="
                currentPage === 1
                  ? 'bg-gray-300 cursor-not-allowed'
                  : 'bg-blue-500 text-white hover:bg-blue-600'
              "
            >
              Previous
            </button>
            <button
              @click="nextPage"
              :disabled="currentPage === totalPages"
              class="px-3 py-1 rounded text-sm"
              :class="
                currentPage === totalPages
                  ? 'bg-gray-300 cursor-not-allowed'
                  : 'bg-blue-500 text-white hover:bg-blue-600'
              "
            >
              Next
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onUnmounted } from "vue";
import Modal from "./Modal.vue";
import { VueDraggable } from "vue-draggable-plus";

const modalOpen = ref(false);
const people = ref([]);
const timer = ref(0);
const sortingActive = ref(false);
const intervalId = ref(null);

// Pagination
const itemsPerPage = 10;
const currentPage = ref(1);

const totalPages = computed(() =>
  Math.ceil(people.value.length / itemsPerPage)
);

const currentRangeText = computed(() => {
  const rangeSize = 100 / totalPages.value;
  const maxValue = 100 - (currentPage.value - 1) * rangeSize;
  const minValue = maxValue - rangeSize;
  return `${Math.ceil(minValue)} - ${Math.floor(maxValue)} potatoes`;
});

const currentPageItems = computed({
  get: () => {
    const start = (currentPage.value - 1) * itemsPerPage;
    const end = start + itemsPerPage;
    return people.value.slice(start, end);
  },
  set: (newItems) => {
    const start = (currentPage.value - 1) * itemsPerPage;
    const newPeople = [...people.value];
    newPeople.splice(start, itemsPerPage, ...newItems);
    people.value = newPeople;
  },
});

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};

const generateRandomData = (count) => {
  const data = [];
  const tableSize = 10;
  const numTables = Math.ceil(count / tableSize);
  const rangeSize = Math.floor(100 / numTables);

  for (let tableIndex = 0; tableIndex < numTables; tableIndex++) {
    const minValue = 100 - (tableIndex + 1) * rangeSize;
    const maxValue = 100 - tableIndex * rangeSize;
    const tableCount = Math.min(tableSize, count - tableIndex * tableSize);
    const usedPotatoes = new Set();

    for (let i = 0; i < tableCount; i++) {
      let potatoes;
      do {
        potatoes =
          Math.floor(Math.random() * (maxValue - minValue + 1)) + minValue;
      } while (usedPotatoes.has(potatoes));
      usedPotatoes.add(potatoes);

      const globalIndex = tableIndex * tableSize + i;
      data.push({
        email: `user${globalIndex}@example.com`,
        potatoes,
        name: `User ${globalIndex}`,
      });
    }
  }
  return data;
};

const openModal = () => (modalOpen.value = true);
const closeModal = () => (modalOpen.value = false);

const startSorting = (count) => {
  const data = generateRandomData(count);
  people.value = data;
  modalOpen.value = false;
  timer.value = 0;
  sortingActive.value = true;
  currentPage.value = 1;
  intervalId.value = setInterval(() => (timer.value += 1), 1000);
};

const stopSorting = () => {
  clearInterval(intervalId.value);
  intervalId.value = null;
  timer.value = 0;
  sortingActive.value = false;
  people.value = [];
  currentPage.value = 1;
};

const handlePageUpdate = () => {
  if (isListSorted()) {
    clearInterval(intervalId.value);
    const elapsedSeconds = timer.value;
    sortingActive.value = false;

    setTimeout(() => {
      alert(`Success! You sorted the list in ${elapsedSeconds} seconds.`);
      people.value = [];
      timer.value = 0;
      currentPage.value = 1;
    }, 50);
  }
};

const isListSorted = () => {
  return people.value.every(
    (person, i, arr) => i === 0 || arr[i - 1].potatoes >= person.potatoes
  );
};

const autoSort = () => {
  people.value = [...people.value].sort((a, b) => b.potatoes - a.potatoes);
  clearInterval(intervalId.value);
  const elapsedSeconds = timer.value;
  sortingActive.value = false;

  setTimeout(() => {
    alert(`Success! You sorted the list in ${elapsedSeconds} seconds.`);
    people.value = [];
    timer.value = 0;
    currentPage.value = 1;
  }, 50);
};

const checkOrder = () => {
  if (isListSorted()) {
    clearInterval(intervalId.value);
    const elapsedSeconds = timer.value;
    sortingActive.value = false;

    setTimeout(() => {
      alert(`Success! You sorted the list in ${elapsedSeconds} seconds.`);
      people.value = [];
      timer.value = 0;
      currentPage.value = 1;
    }, 50);
  } else {
    alert("The list is not sorted correctly. Keep trying");
  }
};

watch(people, () => {
  if (sortingActive.value && isListSorted()) {
    clearInterval(intervalId.value);
    const elapsedSeconds = timer.value;
    sortingActive.value = false;

    setTimeout(() => {
      alert(`Success! You sorted the list in ${elapsedSeconds} seconds.`);
      people.value = [];
      timer.value = 0;
      currentPage.value = 1;
    }, 50);
  }
});

onUnmounted(() => {
  clearInterval(intervalId.value);
});
</script>
