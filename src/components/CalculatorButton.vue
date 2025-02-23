<script setup>
const props = defineProps({
  name: {
    type: String,
  },
  handleButton: {
    type: Function,
  },
})
const handleClick = () => {
  props.handleButton(props.name)
}
</script>
<template>
  <button @click="handleClick">
    <slot></slot>
  </button>
</template>
<style scoped></style>
