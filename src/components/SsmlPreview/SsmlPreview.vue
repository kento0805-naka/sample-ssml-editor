<template>
    <div>
        <p>{{ content }}</p>
    </div>
</template>

<script setup lang="ts">
const props = defineProps<{
  content: string
}>()
</script>

<style scoped lang="scss">
p {
  font-size: 12px;
}
</style>
