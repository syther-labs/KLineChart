<template>
  <div v-if="visible" class="appearance-settings">
    <div class="section">
      <div class="section-title">{{ i18n('component_appearance_mode', lang) }}</div>
      <div class="mode-options">
        <button type="button" :class="{ active: !isDark }" @click="setAppearance(false)">
          <span class="vpi-sun"/>
          <span>{{ i18n('component_appearance_light', lang) }}</span>
        </button>
        <button type="button" :class="{ active: isDark }" @click="setAppearance(true)">
          <span class="vpi-moon"/>
          <span>{{ i18n('component_appearance_dark', lang) }}</span>
        </button>
      </div>
    </div>
    <div class="divider"/>
    <div class="header">
      <span>{{ i18n('component_color_palette_title', lang) }}</span>
      <button type="button" class="reset" @click="resetColor">{{ i18n('component_color_palette_reset', lang) }}</button>
    </div>
    <div
      ref="trackRef"
      class="track-wrap"
      @mousedown="startDrag"
      @touchstart.prevent="startDrag">
      <div class="track"/>
      <button
        type="button"
        class="thumb"
        :style="{ left: `${sliderPercent}%`, backgroundColor: activeColor }"
        @mousedown.stop="startDrag"
        @touchstart.stop.prevent="startDrag"/>
    </div>
    <div class="preset">
      <button
        v-for="color in colors"
        :key="color"
        type="button"
        class="color-item"
        :class="{ active: activeColor === color }"
        :style="{ color }"
        @click="selectPreset(color)">
        <span/>
      </button>
    </div>
  </div>
</template>

<script setup>
import { computed, inject, ref, watch, onMounted, onBeforeUnmount } from 'vue'
import { inBrowser, useData } from 'vitepress'
import { useLocalStorage } from '@vueuse/core'
import i18n from '../@i18n'
import {
  applyThemeColorStyle,
  DEFAULT_THEME_COLOR,
  normalizeThemeColor,
  THEME_COLOR_STORAGE_KEY
} from '../.vitepress/theme/theme-color'

const visible = ref(false)
const { isDark, lang } = useData()
const toggleAppearance = inject('toggle-appearance', null)

const trackRef = ref(null)

const currentColor = useLocalStorage(THEME_COLOR_STORAGE_KEY)
const previewColor = ref(null)
const sliderValue = ref(0)
const dragging = ref(false)

const colors = [
  '#F92855',
  '#EC4899',
  '#F17313',
  '#E6AC00',
  '#2DC08E',
  '#84CC16',
  '#1677FF',
  '#3FB5FB',
  '#A14DFD',
  '#8F6CEE'
]

const finalColor = computed(() => normalizeThemeColor(currentColor.value) || DEFAULT_THEME_COLOR)
const activeColor = computed(() => previewColor.value || finalColor.value)
const sliderPercent = computed(() => sliderValue.value * 100)

onMounted(() => {
  visible.value = true
  sliderValue.value = getColorValue(finalColor.value)
})

watch(finalColor, (color) => {
  if (!dragging.value) {
    sliderValue.value = getColorValue(color)
  }
})

watch(activeColor, (color) => {
  applyThemeColorStyle(color || DEFAULT_THEME_COLOR)
}, { immediate: inBrowser, flush: 'post' })

function changePrimaryColor (color) {
  currentColor.value = color
}

function setAppearance (dark) {
  if (isDark.value === dark) {
    return
  }
  if (toggleAppearance) {
    toggleAppearance()
  } else {
    isDark.value = dark
  }
}

function selectPreset (color) {
  previewColor.value = null
  sliderValue.value = getColorValue(color)
  changePrimaryColor(color)
}

function resetColor () {
  previewColor.value = null
  sliderValue.value = getColorValue(DEFAULT_THEME_COLOR)
  currentColor.value = DEFAULT_THEME_COLOR
}

function startDrag (event) {
  dragging.value = true
  updateSlider(event)
  window.addEventListener('mousemove', onDragMove)
  window.addEventListener('mouseup', onDragEnd)
  window.addEventListener('touchmove', onDragMove, { passive: false })
  window.addEventListener('touchend', onDragEnd)
}

function onDragMove (event) {
  if (!dragging.value) {
    return
  }
  if (event.type === 'touchmove') {
    event.preventDefault()
  }
  updateSlider(event)
}

function onDragEnd () {
  if (!dragging.value) {
    return
  }
  stopDrag()
  changePrimaryColor(activeColor.value)
  previewColor.value = null
}

function stopDrag () {
  dragging.value = false
  window.removeEventListener('mousemove', onDragMove)
  window.removeEventListener('mouseup', onDragEnd)
  window.removeEventListener('touchmove', onDragMove)
  window.removeEventListener('touchend', onDragEnd)
}

function updateSlider (event) {
  if (!trackRef.value) {
    return
  }
  const point = event.touches?.[0] || event
  const rect = trackRef.value.getBoundingClientRect()
  const ratio = clamp((point.clientX - rect.left) / rect.width, 0, 1)
  sliderValue.value = ratio
  previewColor.value = getColorFromValue(ratio)
}

function getColorValue (color) {
  const index = colors.indexOf(color)
  if (index >= 0) {
    return index / (colors.length - 1)
  }
  return findNearestValue(color)
}

function getColorFromValue (value) {
  const scaled = value * (colors.length - 1)
  const leftIndex = Math.floor(scaled)
  const rightIndex = Math.min(colors.length - 1, leftIndex + 1)
  const ratio = scaled - leftIndex
  const left = colors[leftIndex]
  const right = colors[rightIndex]
  if (left === right) {
    return left
  }
  return mixHexColor(left, right, ratio)
}

function mixHexColor (left, right, ratio) {
  const l = hexToRgb(left)
  const r = hexToRgb(right)
  const red = Math.round(l.r + (r.r - l.r) * ratio)
  const green = Math.round(l.g + (r.g - l.g) * ratio)
  const blue = Math.round(l.b + (r.b - l.b) * ratio)
  return rgbToHex(red, green, blue)
}

function hexToRgb (hex) {
  const cleanHex = hex.replace('#', '')
  return {
    r: parseInt(cleanHex.slice(0, 2), 16),
    g: parseInt(cleanHex.slice(2, 4), 16),
    b: parseInt(cleanHex.slice(4, 6), 16)
  }
}

function rgbToHex (r, g, b) {
  return (
    '#' +
    [r, g, b]
      .map(x => x.toString(16).padStart(2, '0'))
      .join('')
  ).toUpperCase()
}

function findNearestValue (color) {
  const target = hexToRgb(color)
  const steps = 240
  let bestValue = getColorValue(DEFAULT_THEME_COLOR)
  let bestDistance = Number.POSITIVE_INFINITY
  for (let i = 0; i <= steps; i++) {
    const value = i / steps
    const candidate = hexToRgb(getColorFromValue(value))
    const distance = colorDistance(target, candidate)
    if (distance < bestDistance) {
      bestDistance = distance
      bestValue = value
    }
  }
  return bestValue
}

function colorDistance (left, right) {
  const dr = left.r - right.r
  const dg = left.g - right.g
  const db = left.b - right.b
  return dr * dr + dg * dg + db * db
}

function clamp (value, min, max) {
  return Math.min(max, Math.max(min, value))
}

onBeforeUnmount(() => {
  stopDrag()
})

</script>

<style scoped>
  .appearance-settings {
    width: 216px;
  }

  .section-title,
  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    color: var(--vp-c-text-1);
    font-size: 12px;
    font-weight: 600;
  }

  .section-title {
    margin-bottom: 8px;
  }

  .mode-options {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 6px;
  }

  .mode-options button {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 6px;
    height: 32px;
    border: 1px solid var(--vp-c-divider);
    border-radius: 6px;
    color: var(--vp-c-text-2);
    background: var(--vp-c-bg-soft);
    cursor: pointer;
  }

  .mode-options button.active {
    border-color: var(--vp-c-indigo-1);
    color: var(--vp-c-text-1);
    box-shadow: inset 0 0 0 1px var(--vp-c-indigo-1);
  }

  .divider {
    height: 1px;
    margin: 12px 0;
    background: var(--vp-c-divider);
  }

  .reset {
    border: none;
    background: transparent;
    color: var(--vp-c-indigo-1);
    cursor: pointer;
    padding: 0;
    font-size: 12px;
  }

  .track-wrap {
    position: relative;
    height: 18px;
    margin: 2px 2px 14px;
    cursor: pointer;
    user-select: none;
  }

  .track {
    position: absolute;
    top: 50%;
    left: 0;
    right: 0;
    height: 8px;
    border-radius: 999px;
    transform: translateY(-50%);
    background: linear-gradient(
      90deg,
      #F92855 0%,
      #EC4899 11%,
      #F17313 22%,
      #E6AC00 33%,
      #2DC08E 44%,
      #84CC16 55%,
      #1677FF 66%,
      #3FB5FB 77%,
      #A14DFD 88%,
      #8F6CEE 100%
    );
  }

  .thumb {
    position: absolute;
    top: 50%;
    width: 16px;
    height: 16px;
    border-radius: 50%;
    border: solid 2px #fff;
    box-shadow: 0 0 0 1px rgba(0, 0, 0, .18), 0 3px 8px rgba(0, 0, 0, .16);
    transform: translate(-50%, -50%);
    cursor: grab;
    padding: 0;
  }

  .thumb:active {
    cursor: grabbing;
  }

  .preset {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 8px;
  }

  .color-item {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 24px;
    height: 24px;
    padding: 0;
    border-radius: 50%;
    border: solid 1px transparent;
    box-sizing: border-box;
    background: transparent;
    cursor: pointer;
  }

  .color-item span {
    width: 14px;
    height: 14px;
    border-radius: 50%;
    background-color: currentColor;
  }

  .color-item.active {
    border-color: currentColor;
    box-shadow: 0 0 0 1px currentColor;
  }

</style>
