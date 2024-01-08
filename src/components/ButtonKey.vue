<script setup lang="ts">
const props = defineProps<{
   keys: [number, number]
}>()

const emit = defineEmits<{
   (e: 'click', keys: typeof props.keys): void
}>()

function handleClick() {
   emit('click', props.keys)
}

</script>

<template>
   <button @click="handleClick">
      {{ props.keys[0] }}
      ou
      {{ props.keys[1] }}
   </button>
</template>

<style></style>