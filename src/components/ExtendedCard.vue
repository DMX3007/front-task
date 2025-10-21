<template>
  <div class="flex items-center gap-3">
    <div class="text-[#1E0E4C]">
      <div
        class="w-[80px] h-[80px] text-base rounded-full overflow-hidden bg-gray-300"
        :class="[isFocused ? `outline-[#906FEE] outline-[1.5px] outline-offset-2` : '']"
      >
        <img
          v-if="imageUrl"
          :src="imageUrl"
          :alt="name"
        />
      </div>
    </div>

    <div class="flex flex-col gap-2">
      <label
        :for="index.toString()"
        :class="['font-koulen font-normal uppercase text-base cursor-pointer', isFocused ? 'text-[#5D3FD3]': 'text-[#1E0E4C]']"
      >
        {{ name }} is
      </label>

      <div class="flex items-center gap-2">
        <input
          :id="index.toString()"
          ref="inputEl"
          v-model="displayValue"
          type="text"
          inputmode="numeric"
          :style="{ width: inputWidth + 'px' }"
          class="
            px-[5px]
            w-[72px]
            h-[44px]
            outline outline-[#CFCADF]
            rounded-[6px]
            text-base
            transition-all duration-200
            focus:outline-[#906FEE]
            focus:outline-[1.5px]
            focus:ring-opacity-20
            font-inter
            font-medium
          "
          @input="maskInput"
          @focus="isFocused = true"
          @blur="isFocused = false"
        />
        <span class="text-[#1E0E4C] text-base font-inter">hours old</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, nextTick } from 'vue'

interface Props {
  name: string
  index: number,
  modelValue: string | number | null
  imageUrl?: string
}

const props = withDefaults(defineProps<Props>(), {
})

const emit = defineEmits<{
  'update:modelValue': [value: string]
}>()

const inputEl = ref<HTMLInputElement | null>(null)
const displayValue = ref('')
const inputWidth = ref(72)
const isFocused = ref(false)

const formatNumber = (value: string): string => {
  const digits = value.replace(/\D/g, '')
  return digits.replace(/\B(?=(\d{3})+(?!\d))/g, ' ')
}

const updateWidth = (text: string) => {
  const minWidth = 72
  const charWidth = 8.5
  const padding = 12

  // Calculate space for text
  const textWidth = text.length * charWidth
  const requiredWidth = textWidth + padding

  if (requiredWidth <= minWidth) {
    inputWidth.value = minWidth
  } else {
    inputWidth.value = Math.ceil(Math.min(requiredWidth, 300))
  }
}

const maskInput = (e: Event) => {
  const target = e.target as HTMLInputElement
  const cursorPos = target.selectionStart || 0
  const oldValue = displayValue.value

  const digits = target.value.replace(/\D/g, '')

  const formatted = formatNumber(digits)
  displayValue.value = formatted

  updateWidth(formatted)

  emit('update:modelValue', digits)

  nextTick(() => {
    if (inputEl.value) {
      const oldSpaces = oldValue.substring(0, cursorPos).split(' ').length - 1
      const newSpaces = formatted.substring(0, cursorPos).split(' ').length - 1
      const newPos = cursorPos + (newSpaces - oldSpaces)
      inputEl.value.setSelectionRange(newPos, newPos)
    }
  })
}

watch(() => props.modelValue, (val) => {
  const formatted = formatNumber(String(val || ''))
  displayValue.value = formatted
  updateWidth(formatted)
}, { immediate: true })
</script>
