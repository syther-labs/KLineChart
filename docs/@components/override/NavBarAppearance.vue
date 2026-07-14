<script setup>
import { ref, computed } from 'vue'
import { useData } from 'vitepress'
import VPFlyout from 'vitepress/dist/client/theme-default/components/VPFlyout.vue'

import AppearanceSetting from './AppearanceSetting.vue'

const { site } = useData()

const icon = `<svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true" focusable="false">
        <path d="M7.02 3.635l12.518 12.518a1.863 1.863 0 010 2.635l-1.317 1.318a1.863 1.863 0 01-2.635 0L3.068 7.588A2.795 2.795 0 117.02 3.635zm2.09 14.428a.932.932 0 110 1.864.932.932 0 010-1.864zm-.043-9.747L7.75 9.635l9.154 9.153 1.318-1.317-9.154-9.155zM3.52 12.473c.514 0 .931.417.931.931v.932h.932a.932.932 0 110 1.864h-.932v.931a.932.932 0 01-1.863 0l-.001-.931h-.93a.932.932 0 010-1.864h.93v-.932c0-.514.418-.931.933-.931zm15.374-3.727a1.398 1.398 0 110 2.795 1.398 1.398 0 010-2.795zM4.385 4.953a.932.932 0 000 1.317l2.046 2.047L7.75 7 5.703 4.953a.932.932 0 00-1.318 0zM14.701.36a.932.932 0 01.931.932v.931h.932a.932.932 0 010 1.864h-.933l.001.932a.932.932 0 11-1.863 0l-.001-.932h-.93a.932.932 0 110-1.864h.93v-.931a.932.932 0 01.933-.932z"/>
      </svg>`
</script>

<template>
  <VPFlyout
    v-if="
      site.appearance &&
      site.appearance !== 'force-dark' &&
      site.appearance !== 'force-auto'
    "
    :button="icon"
    :hiddenArrow="true"
    class="NavBarAppearance">
    <AppearanceSetting/>
  </VPFlyout>
</template>

<style scoped>
.NavBarAppearance {
  position: relative;
  display: none;
}

.NavBarAppearance :deep(.button) {
  padding: 0 4px
}

@media (min-width: 1280px) {
  .NavBarAppearance {
    display: flex;
    align-items: center;
  }
}
</style>
