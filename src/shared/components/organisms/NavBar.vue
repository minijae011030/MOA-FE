<template>
  <nav class="fixed bottom-0 left-0 w-full bg-white border-t z-50">
    <div class="flex justify-around items-center h-[60px] max-w-[390px] w-full mx-auto">
      <button
        v-for="tab in tabs"
        :key="tab.value"
        :class="[
          'flex flex-col items-center justify-center',
          isActive(tab.value) && 'text-moa-main',
        ]"
        @click="selectTab(tab.value)"
      >
        <i
          :class="[
            'bi text-2xl transition',
            tab.icon,
            isActive(tab.value) ? 'text-moa-main' : 'text-black',
          ]"
        />
      </button>
    </div>
  </nav>
</template>

<script setup lang="ts">
import { useRoute, useRouter } from 'vue-router'

const router = useRouter()
const route = useRoute()

defineProps<{
  modelValue: string
}>()

const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void
}>()

const tabs = [
  { icon: 'bi-airplane', value: 'trip', location: '/trip/trip_list' },
  { icon: 'bi-house', value: 'home', location: '/main/home' },
  { icon: 'bi-pin-map', value: 'record', location: '/record/map' },
]

const isActive = (tabValue: string): boolean => {
  const path = route.path

  if (tabValue === 'trip') return path.startsWith('/trip')
  if (tabValue === 'home') return path.startsWith('/main')
  if (tabValue === 'record') return path.startsWith('/record')
  return false
}
const selectTab = (tab: string) => {
  const selected = tabs.find((t) => t.value === tab)
  if (!selected) return

  emit('update:modelValue', tab)
  router.push(selected.location)
}
</script>
