<template>
  <div class="mb-4 bg-white rounded-lg overflow-hidden shadow">
    <div
      class="flex items-center justify-between p-4 bg-gray-50 cursor-pointer"
      @click="toggleCollapsed"
    >
      <h3 class="font-medium">Table {{ tableIndex + 1 }}</h3>
      <button class="text-gray-500 hover:text-gray-700">
        {{ isCollapsed ? "Expand" : "Collapse" }}
      </button>
    </div>

    <div v-show="!isCollapsed">
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
        <VueDraggable v-model="localTableData" tag="tbody" @change="emitUpdate">
          <tr
            v-for="person in localTableData"
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

<script setup>
import { ref, computed } from "vue";
import { VueDraggable } from "vue-draggable-plus";

const props = defineProps({
  tableData: {
    type: Array,
    required: true,
  },
  tableIndex: {
    type: Number,
    required: true,
  },
});

const emit = defineEmits(["update"]);

const localTableData = computed({
  get: () => props.tableData,
  set: (newValue) => {
    emit("update", props.tableIndex, newValue);
  },
});

const isCollapsed = ref(false);

const toggleCollapsed = () => {
  isCollapsed.value = !isCollapsed.value;
};

const emitUpdate = () => {
  emit("update", props.tableIndex, props.tableData);
};
</script>
