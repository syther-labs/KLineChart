<script setup>
import { computed } from 'vue'
import { useData, withBase } from 'vitepress'

import i18n from '../../@i18n'
import Section from './Section.vue'
import { useInView } from './composables/useInView.js'

const { lang, isDark } = useData()
const { target: casesRef, isVisible } = useInView()

const items = computed(() =>
  [1, 2, 3, 4].map(index => ({
    variant: index,
    title: i18n(`view_home_case_${index}_title`, lang.value),
    description: i18n(`view_home_case_${index}_desc`, lang.value)
  }))
)
</script>

<template>
  <Section
    out-class="use-cases-section"
    :title="i18n('view_home_case_title', lang)"
    :description="i18n('view_home_case_desc', lang)"
  >
    <div ref="casesRef" class="use-cases home-stagger" :class="{ 'is-visible': isVisible }">
      <article
        v-for="(item, index) in items"
        :key="item.title"
        class="use-case home-card home-stagger-item"
        :style="{ '--stagger-delay': `${index * 0.07}s` }"
      >
        <div class="use-case-body home-card-body">
          <h3>{{ item.title }}</h3>
          <p class="summary">{{ item.description }}</p>
        </div>
        <div class="illustration-wrap" aria-hidden="true">
          <img
            class="illustration"
            :src="withBase(`/images/use-cases/use-case-${item.variant}-${isDark ? 'dark' : 'light'}.png`)"
            :alt="''"
            width="160"
            height="120"
            loading="lazy"
          >
        </div>
      </article>
    </div>
  </Section>
</template>

<style scoped>
.use-cases {
  display: grid;
  width: 100%;
  gap: var(--home-grid-gap);
}

.use-case {
  display: flex;
  flex-direction: row;
  align-items: stretch;
  min-height: 100%;
  padding: 0;
  overflow: hidden;
}

.use-case-body {
  flex: 1;
  min-width: 0;
  justify-content: center;
}

.summary {
  flex: 1;
  margin: 0;
  font-size: 14px;
  line-height: 24px;
}

.illustration-wrap {
  flex: 0 0 clamp(96px, 34%, 176px);
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100%;
  padding: 12px 8px;
  overflow: hidden;
}

.illustration {
  display: block;
  width: 100%;
  max-width: 160px;
  height: auto;
  aspect-ratio: 4 / 3;
  object-fit: contain;
}

@media (min-width: 960px) {
  .use-cases {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: var(--home-grid-gap-lg);
  }

  .illustration-wrap {
    padding: 20px 14px;
  }
}
</style>
