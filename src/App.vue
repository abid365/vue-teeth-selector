<script setup lang="ts">
import { ref } from "vue";
import TeethDiagram from "./lib/TeethDiagram.vue";

const selectedTeeth = ref<Record<string, boolean>>({
  "tooth-11": true,
});

const handleTeethChange = (map: Record<string, boolean>, info: any) => {
  console.log("Teeth change:", map, info);
  if (!info.isSelected) {
    selectedTeeth.value = { ...map, [info.id]: true };
  } else {
    selectedTeeth.value = { ...map, [info.id]: false };
  }
};
</script>

<template>
  <div>
    <h1>Vue Teeth Selector Demo</h1>
    <div class="odontogram">
      <TeethDiagram
        :selectedTeeth="selectedTeeth"
        @change="handleTeethChange"
      />
    </div>
    <div style="margin-top: 20px">
      <h3>Selected Teeth:</h3>
      <pre>{{ JSON.stringify(selectedTeeth, null, 2) }}</pre>
    </div>
  </div>
</template>

<style scoped>
h1 {
  text-align: center;
  margin-bottom: 14px;
}
pre {
  background: #f5f5f5;
  padding: 10px;
  border-radius: 4px;
}
.odontogram {
  display: flex;
  justify-content: center;
  justify-items: center;
  margin-top: 10px;
  margin-bottom: 5px;
}
</style>
