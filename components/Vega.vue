<template>
    <div ref="vega"></div>
</template>
<script setup lang="ts">
import embed from 'vega-embed'
import { onMounted, ref, watchEffect } from 'vue'

const props = defineProps({
  spec: {
    default: () => {},
  },
})
const vega = ref<HTMLDivElement>()

watchEffect(async () => {
  if (vega.value === null) {
    return
  }
    await embed(vega.value, props.spec, {mode: "vega",renderer: "svg", actions:false})
})
</script>
