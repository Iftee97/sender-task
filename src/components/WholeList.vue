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

    <div v-if="tables.length > 0">
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
        <span>{{ totalPeople }} people in {{ tables.length }} tables</span>
      </div>

      <VueDraggable v-model="tables" class="space-y-4">
        <SortableTable
          v-for="(table, index) in tables"
          :key="index"
          :table-data="table"
          :table-index="index"
          @update="handleTableUpdate"
        />
      </VueDraggable>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onUnmounted } from "vue";
import Modal from "./Modal.vue";
import SortableTable from "./SortableTable.vue";
import { VueDraggable } from "vue-draggable-plus";

const modalOpen = ref(false);
const tables = ref([]);
const timer = ref(0);
const sortingActive = ref(false);
const intervalId = ref(null);

const totalPeople = computed(() => {
  return tables.value.reduce((sum, table) => sum + table.length, 0);
});

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

const splitIntoTables = (data) => {
  const result = [];
  for (let i = 0; i < data.length; i += 10) {
    result.push(data.slice(i, i + 10));
  }
  return result;
};

const openModal = () => (modalOpen.value = true);
const closeModal = () => (modalOpen.value = false);

const startSorting = (count) => {
  const data = generateRandomData(count);
  tables.value = splitIntoTables(data);
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
  tables.value = [];
};

const handleTableUpdate = (tableIndex, newData) => {
  tables.value[tableIndex] = newData;
};

const autoSort = () => {
  // First sort each table
  tables.value = tables.value.map((table) =>
    [...table].sort((a, b) => b.potatoes - a.potatoes)
  );

  // Then sort the tables themselves
  tables.value.sort((a, b) => {
    const aMax = Math.max(...a.map((p) => p.potatoes));
    const bMax = Math.max(...b.map((p) => p.potatoes));
    return bMax - aMax;
  });

  clearInterval(intervalId.value);
  const elapsedSeconds = timer.value;
  sortingActive.value = false;

  setTimeout(() => {
    alert(`Success! You sorted the list in ${elapsedSeconds} seconds.`);
    tables.value = [];
    timer.value = 0;
  }, 50);
};

const checkOrder = () => {
  // Check if each table is sorted internally
  const areTablesSorted = tables.value.every((table) =>
    table.every(
      (person, i, arr) => i === 0 || arr[i - 1].potatoes >= person.potatoes
    )
  );

  // Check if tables are sorted relative to each other
  const areTablesInOrder = tables.value.every((table, i, arr) => {
    if (i === 0) return true;
    const prevTableMin = Math.min(...arr[i - 1].map((p) => p.potatoes));
    const currentTableMax = Math.max(...table.map((p) => p.potatoes));
    return prevTableMin >= currentTableMax;
  });

  if (areTablesSorted && areTablesInOrder) {
    clearInterval(intervalId.value);
    const elapsedSeconds = timer.value;
    sortingActive.value = false;

    setTimeout(() => {
      alert(`Success! You sorted the list in ${elapsedSeconds} seconds.`);
      tables.value = [];
      timer.value = 0;
    }, 50);
  } else {
    alert("The list is not sorted correctly. Keep trying");
  }
};

onUnmounted(() => {
  clearInterval(intervalId.value);
});
</script>
