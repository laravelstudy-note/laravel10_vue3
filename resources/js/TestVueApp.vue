<script lang="ts" setup>
import { ref } from 'vue'

const count = ref(0)
let title : string = "typescript";

function increment() {
  count.value++
}
</script>

<template>
  <!-- make this button work -->
  <button @click="increment">count is: {{ count }}</button>
  <p>{{ title }}</p>
</template>