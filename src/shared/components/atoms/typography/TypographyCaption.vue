<template>
  <BaseTypography
    type="Caption"
    :class="className"
  >
    <slot />
  </BaseTypography>
</template>

<script setup lang="ts">
import BaseTypography from './BaseTypography.vue'

defineProps<{
  className?: string
}>()
</script>
