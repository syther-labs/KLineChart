<script setup>
import ChartPreview from '../../../../@components/ChartPreview.vue';
import { data } from './index.data'
</script>

<template>
  <ChartPreview
    title="isZoomEnabled"
    chartId="isZoomEnabled-chart"
    :code="data.js"/>
</template>