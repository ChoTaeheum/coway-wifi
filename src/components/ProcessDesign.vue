<template>
  <div class="name">
    {{ name }}
  </div>
  <button class="btn btn-primary" @click="updateName">Click</button>
</template>

<script>
import { ref } from "vue";
export default {
  setup() {
    const name = ref("Taem");

    const updateName = () => {
      name.value = "TaemTaem";
    };

    return {
      name,
      updateName,
    };
  },
};
</script>

<style>
</style>