<template>
  <SeparateSortableTables />
</template>

<script setup>
import SeparateSortableTables from "./components/SeparateSortableTables.vue"; // separate sortable tables (jumbled)
import SeparateSortableTables_Prev from "./components/SeparateSortableTables_Prev.vue"; // separate sortable tables (in order)
import RangeBasedPaginatedList from "./components/RangeBasedPaginatedList.vue"; // range-based pagination

// previous versions
import PrevWholeList from "./components/PrevWholeList.vue"; // entire list
import PaginatedList from "./components/PaginatedList.vue"; // paginated list
</script>
