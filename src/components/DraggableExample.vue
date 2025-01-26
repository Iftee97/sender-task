<template>
  <div class="p-4 max-w-4xl mx-auto">
    <VueDraggable
      v-model="list"
      animation="150"
      ghostClass="ghost"
      class="container"
    >
      <div v-for="item in list" :key="item.id" class="item">
        {{ item.name }}
      </div>
    </VueDraggable>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { VueDraggable } from "vue-draggable-plus";

const list = ref([
  {
    name: "Joao",
    id: 1,
  },
  {
    name: "Jean",
    id: 2,
  },
  {
    name: "Johanna",
    id: 3,
  },
  {
    name: "Juan",
    id: 4,
  },
]);
</script>

<style scoped>
.container {
  display: grid;
  gap: 10px;
}
.item {
  width: 200px;
  background-color: red;
  height: 30px;
  line-height: 30px;
  text-align: center;
  cursor: move;
}
</style>
