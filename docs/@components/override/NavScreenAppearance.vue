<script setup>
import { useData } from 'vitepress'
import AppearanceSetting from './AppearanceSetting.vue'

const { site, theme } = useData()
</script>

<template>
  <div
    v-if="
      site.appearance &&
      site.appearance !== 'force-dark' &&
      site.appearance !== 'force-auto'
    "
    class="NavScreenAppearance"
  >
    <AppearanceSetting/>
  </div>
</template>

<style scoped>
.NavScreenAppearance {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-radius: 8px;
  padding: 12px 14px 12px 16px;
  background-color: var(--vp-c-bg-soft);
}
</style>
