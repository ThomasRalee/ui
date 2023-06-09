<script setup lang="ts">
import { ref, PropType } from 'vue'
import { onClickOutside, onKeyStroke } from '@vueuse/core'

const emit = defineEmits<{
  (e: 'close'): void
}>()

const props = defineProps({
  show: Boolean,
  showLoading: Boolean,

  ignore: {
    type: Array as PropType<string[]>,
    default: () => []
  },

  containerClass: {
    type: String,
    default: ''
  },

  wrapperClass: {
    type: String,
    default: 'bg-gray-900 bg-opacity-80'
  }
})

const modalRef = ref(null)

onKeyStroke('Escape', () => {
  if (props.show) {
    close()
  }
})

onClickOutside(
  modalRef,
  () => {
    close()
  },
  {
    ignore: ['.modal-content', ...props.ignore]
  }
)

const close = () => {
  emit('close')
}
</script>
<script lang="ts">
export default {
  inheritAttrs: false
}
</script>

<template>
  <Transition name="modal" appear>
    <div
      v-if="show"
      class="fixed inset-0 z-50 h-full w-full duration-300 ease-in"
      :class="wrapperClass"
    >
      <div class="flex items-center justify-center h-full">
        <div
          ref="modalRef"
          class="modal-container overflow-y-hidden"
          :class="$attrs.class"
        >
          <div
            class="max-h-screen sm:max-h-[90vh] overflow-y-auto"
            :class="containerClass"
          >
            <div class="modal-content">
              <slot :close="close" :show-loading="showLoading" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
.modal-enter-from,
.modal-leave-to {
  @apply opacity-0;
}

.modal-leave-active .modal-container {
  transition: 300ms cubic-bezier(0.4, 0, 1, 1);
  transform: scale(0.9);
}
</style>
