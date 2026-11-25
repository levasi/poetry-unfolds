<script setup lang="ts">
import type { CarouselTheme } from '~/composables/useCarouselGenerator'
import { CAROUSEL_THEME_IDS } from '~/composables/useCarouselGenerator'

const props = defineProps<{
  modelValue: CarouselTheme
}>()

const emit = defineEmits<{
  'update:modelValue': [value: CarouselTheme]
}>()

const { t } = useI18n()

function onChange(e: Event) {
  const v = (e.target as HTMLSelectElement).value
  if ((CAROUSEL_THEME_IDS as readonly string[]).includes(v)) {
    emit('update:modelValue', v as CarouselTheme)
  }
}
</script>

<template>
  <select
    class="ds-input carousel-font-select"
    :value="props.modelValue"
    @change="onChange"
  >
    <option
      v-for="id in CAROUSEL_THEME_IDS"
      :key="id"
      :value="id"
    >
      {{ t(`carousel.theme.${id}`) }}
    </option>
  </select>
</template>
