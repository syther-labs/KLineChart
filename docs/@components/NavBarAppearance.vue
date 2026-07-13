<script setup>
import { ref } from 'vue'
import { useData } from 'vitepress'

import AppearanceSetting from './AppearanceSetting.vue'

const { site, theme } = useData()
const rootRef = ref(null)
const open = ref(false)

function onFocusOut (event) {
  if (!rootRef.value?.contains(event.relatedTarget)) {
    open.value = false
  }
}
</script>

<template>
  <div
    v-if="
      site.appearance &&
      site.appearance !== 'force-dark' &&
      site.appearance !== 'force-auto'
    "
    class="NavBarAppearance"
    ref="rootRef"
    @mouseenter="open = true"
    @mouseleave="open = false"
    @focusin="open = true"
    @focusout="onFocusOut"
    @keydown.esc="open = false"
  >
    <button
      type="button"
      class="button"
      aria-haspopup="true"
      :aria-expanded="open"
      :aria-label="theme.darkModeSwitchLabel || 'Appearance'"
      @click="open = !open">
      <svg viewBox="0 0 1024 1024" width="20" height="20" fill="currentColor" aria-hidden="true">
        <path d="M443.904 956.416c-6.144 0-12.8-0.512-18.944-2.048-116.736-23.552-221.696-94.72-287.744-195.584S49.152 537.088 74.752 417.28C105.984 270.848 212.48 147.968 353.28 96.256c135.68-49.664 281.6-34.304 400.896 43.008 118.272 76.8 195.072 205.312 205.312 344.576 4.096 64-17.408 122.88-61.44 168.448-46.08 48.128-109.056 75.776-177.152 74.24l-183.808-3.072c-13.312-0.512-20.992 5.12-27.136 14.336-6.144 9.216-7.168 20.48-2.56 30.208l23.04 54.272c14.336 33.28 8.704 71.168-14.336 99.328-18.432 22.528-44.544 34.816-72.192 34.816zM512 131.584c-45.568 0-92.16 8.192-136.704 24.576C254.976 200.192 163.84 305.152 137.216 430.592c-22.016 103.424-3.072 207.872 53.248 293.888 56.32 86.528 146.432 147.456 246.784 167.936 15.36 3.584 25.6-6.656 29.184-10.752 8.192-9.728 9.728-22.528 5.12-33.792L448.512 793.6c-12.8-30.208-9.728-64 8.704-91.136 18.432-27.136 47.616-43.008 81.408-42.496l183.808 3.072h3.072c47.616 0 94.208-19.968 126.976-54.272 31.232-32.768 46.592-74.752 44.032-117.76-8.704-121.344-74.24-231.424-176.128-297.472-64-41.472-135.68-61.952-208.384-61.952z"/>
        <path d="M272.896 579.072m-64 0a64 64 0 1 0 128 0 64 64 0 1 0-128 0Z"/>
        <path d="M325.12 369.152m-64 0a64 64 0 1 0 128 0 64 64 0 1 0-128 0Z"/>
        <path d="M513.536 278.528m-64 0a64 64 0 1 0 128 0 64 64 0 1 0-128 0Z"/>
        <path d="M709.12 392.192m-64 0a64 64 0 1 0 128 0 64 64 0 1 0-128 0Z"/>
      </svg>
    </button>
    <div class="menu" :class="{ open }">
      <div class="panel">
        <AppearanceSetting/>
      </div>
    </div>
  </div>
</template>

<style scoped>
.NavBarAppearance {
  position: relative;
  display: none;
}

.button {
  display: flex;
  align-items: center;
  gap: 4px;
  height: var(--vp-nav-height);
  color: var(--vp-c-text-1);
  transition: color .25s;
}

.NavBarAppearance:hover .button,
.button:focus-visible {
  color: var(--vp-c-text-2);
}

.menu {
  position: absolute;
  top: 100%;
  right: 0;
  padding-top: 8px;
  opacity: 0;
  visibility: hidden;
  transform: translateY(-4px);
  pointer-events: none;
  transition: opacity .2s, visibility .2s, transform .2s;
}

.menu.open {
  opacity: 1;
  visibility: visible;
  transform: translateY(0);
  pointer-events: auto;
}

.panel {
  padding: 12px;
  border: 1px solid var(--vp-c-divider);
  border-radius: 8px;
  background-color: var(--vp-c-bg-elv);
  box-shadow: var(--vp-shadow-3);
}

@media (min-width: 1280px) {
  .NavBarAppearance {
    display: flex;
    align-items: center;
  }
}
</style>
