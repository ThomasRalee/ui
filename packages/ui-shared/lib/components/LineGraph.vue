<script lang="ts" setup>
import { PropType, ref, computed, onMounted, onBeforeUnmount } from 'vue'

const props = defineProps({
  color: {
    type: String,
    default: 'black'
  },

  data: {
    type: Array as PropType<Array<number[]>>,
    required: true
  },

  strokeWidth: {
    type: Number,
    default: 2
  }
})

const id = ref(Math.random() + Date.now())
const root = ref(undefined)
const width = ref(0)
const height = ref(0)
const ro = ref(undefined as unknown as ResizeObserver)
const observedElement = ref(undefined as HTMLElement | null | undefined)

onMounted(() => {
  ro.value = new ResizeObserver((entries) => {
    for (const entry of entries) {
      if (entry.contentRect) {
        width.value = entry.contentRect.width
        height.value = entry.contentRect.height
      }
    }
  })

  const rootEl = root.value as HTMLElement | undefined
  if (!rootEl) {
    return
  }

  observedElement.value = rootEl.parentElement
  ro.value.observe(observedElement.value as Element)
})

onBeforeUnmount(() => {
  ro.value.unobserve(observedElement.value as Element)
})

const polyfillPoints = computed(() => {
  const maxX = Math.max(...props.data.map((p: any) => p[0]))
  const maxY = Math.max(...props.data.map((p: any) => p[1]))
  const minY = Math.min(...props.data.map((p: any) => p[1]))

  return props.data.reduce((points, [x, y]) => {
    const pointXInWidth = (x / maxX) * width.value
    const pointYInHeight =
      height.value - ((y - minY) / (maxY - minY)) * height.value

    return `${points} ${pointXInWidth},${pointYInHeight}`
  }, '')
})
</script>

<template>
  <svg
    id="chart"
    ref="root"
    :width="width"
    :height="height"
    :viewBox="`0 0 ${width} ${height}`"
    xmlns="http://www.w3.org/2000/svg"
  >
    <polyline
      :id="`${id}`"
      :points="polyfillPoints"
      style="fill: none; stroke-width: 1"
      :style="{ stroke: color, 'stroke-width': strokeWidth }"
    />
  </svg>
</template>
