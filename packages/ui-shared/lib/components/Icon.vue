<script setup lang="ts">
import { computed, defineAsyncComponent } from 'vue'

const isWebpack = process.env.BUILDER_TYPE === 'webpack'

const props = defineProps({
  xs: Boolean,
  sm: Boolean,
  md: Boolean,
  name: {
    type: String,
    required: true
  }
})

/* temp fix: vite dev not dont support dynamic path
  https://github.com/vitejs/vite/issues/4945
  https://vitejs.dev/guide/features.html#glob-import
*/
const sizeClasses = computed<string>(() => {
  if (props.xs) {
    return 'h-2 w-2 min-w-2'
  }

  if (props.sm) {
    return 'h-3 w-3 min-w-3'
  }

  if (props.md) {
    return 'h-4 w-4 min-w-4'
  }

  return 'h-6 w-6 min-w-6'
})

const dynamicComponent = defineAsyncComponent(() => {
  let name = props.name

  if (name.includes('ledger-legacy')) {
    name = 'wallet/ledger'
  }

  if (name.includes('ledger')) {
    name = 'wallet/ledger'
  }

  return new Promise((resolve, _reject) => {
    if (!isWebpack) {
      const comps = import.meta.glob('./../../lib/icons/**/*.vue')

      return comps[`../icons/${name}.vue`]().then((component: any) =>
        resolve(component.default)
      )
    }

    // webpack
    import(/* @vite-ignore */ `./../../lib/icons/${name}.vue`).then(
      (component) => resolve(component)
    )
  })
})
</script>

<template>
  <component
    :is="dynamicComponent"
    class="cursor-pointer"
    :class="[$attrs.class ? $attrs.class : sizeClasses]"
  />
</template>
