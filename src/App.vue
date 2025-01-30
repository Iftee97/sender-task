<template>
  <div class="p-4 max-w-4xl mx-auto">
    <div class="flex bg-white shadow mb-6">
      <button
        @click="activeTab = 'sortable'"
        class="flex-1 px-6 py-3 text-sm font-medium"
        :class="
          activeTab === 'sortable'
            ? 'bg-white text-black border-b-2 border-black'
            : 'text-gray-500 hover:text-gray-700 bg-gray-50'
        "
      >
        Sortable Tables
      </button>
      <button
        @click="activeTab = 'paginated'"
        class="flex-1 px-6 py-3 text-sm font-medium"
        :class="
          activeTab === 'paginated'
            ? 'bg-white text-black border-b-2 border-black'
            : 'text-gray-500 hover:text-gray-700 bg-gray-50'
        "
      >
        Paginated Table
      </button>
    </div>

    <SeparateSortableTables v-if="activeTab === 'sortable'" />
    <RangeBasedPaginatedList v-else-if="activeTab === 'paginated'" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import SeparateSortableTables from "./components/SeparateSortableTables.vue";
import RangeBasedPaginatedList from "./components/RangeBasedPaginatedList.vue";

const activeTab = ref("sortable");
</script>
