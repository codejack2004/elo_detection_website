<template>
  <div class="hw">
    <h1>{{ msg }}</h1>
  </div>

</template>


<script setup lang="ts">
defineProps<{ msg: string }>()
</script>

<style lang="scss" scoped>
.hw {
  padding: 10px;
  display: flex;
  justify-content: center;
  align-items: center;

  h1 {
    color: red;
  }
}
</style>
