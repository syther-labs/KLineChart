<script setup>
import ChartPreview from '../../../../@components/ChartPreview.vue';
import { data } from './index.data'
</script>

<template>
  <ChartPreview
    title="getPaneOptions"
    chartId="getPaneOptions-chart"
    :code="data.js"/>
</template>