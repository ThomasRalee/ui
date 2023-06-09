<script setup lang="ts">
import { PropType, computed } from 'vue'
import { BaseDropdownOption } from '../types'

const props = defineProps({
  options: {
    type: Array as PropType<BaseDropdownOption[]>,
    required: true
  },

  search: {
    type: String,
    default: ''
  },

  modelValue: {
    type: Array as PropType<string[]>,
    required: true
  },

  contentClasses: {
    type: String,
    default: ''
  }
})

const emits = defineEmits<{
  (e: 'update:modelValue', msg: string[]): void
}>()

const filteredItems = computed(() => {
  return props.options.filter(({ display }) =>
    display.toLowerCase().includes(props.search.toLowerCase())
  )
})

function handleToggleItem(value: string) {
  if (props.modelValue.includes(value)) {
    emits(
      'update:modelValue',
      props.modelValue.filter((message) => message !== value)
    )
  } else {
    emits('update:modelValue', [...props.modelValue!, value])
  }
}
</script>

<template>
  <BaseDropdown>
    <template #default="{ shown }">
      <slot name="default" v-bind="{ shown }" />
    </template>

    <template #content>
      <div :class="contentClasses">
        <slot name="search" />

        <div
          v-for="option in filteredItems"
          :key="`multiselect-item-${option.value}`"
          @click="handleToggleItem(option.value)"
        >
          <slot
            name="item"
            v-bind="{
              option: option,
              isSelected: modelValue.includes(option.value)
            }"
          />
        </div>
      </div>
    </template>
  </BaseDropdown>
</template>
