<template>
  <div class="p-4 max-w-4xl mx-auto">
    <div class="flex space-x-4 mb-6 border-b">
      <button
        @click="activeTab = 'sortable'"
        class="px-4 py-2 -mb-px"
        :class="
          activeTab === 'sortable'
            ? 'text-blue-600 border-b-2 border-blue-600 font-medium'
            : 'text-gray-500 hover:text-gray-700'
        "
      >
        Sortable Tables
      </button>
      <button
        @click="activeTab = 'paginated'"
        class="px-4 py-2 -mb-px"
        :class="
          activeTab === 'paginated'
            ? 'text-blue-600 border-b-2 border-blue-600 font-medium'
            : 'text-gray-500 hover:text-gray-700'
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
