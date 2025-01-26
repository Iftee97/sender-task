<template>
  <WholeList />
</template>

<script setup>
import WholeList from "./components/WholeList.vue"; // separate sortable tables
import PrevWholeList from "./components/PrevWholeList.vue"; // entire list
import PaginatedList from "./components/PaginatedList.vue"; // paginated list
import RangeBasedPaginatedList from "./components/RangeBasedPaginatedList.vue"; // range-based pagination
</script>
