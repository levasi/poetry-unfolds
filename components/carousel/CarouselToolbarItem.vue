<script setup lang="ts">
withDefaults(
  defineProps<{
    label: string
    hint?: string
    /** Where the tooltip appears relative to the control. */
    placement?: 'left' | 'right' | 'bottom'
  }>(),
  { placement: 'bottom' },
)
</script>

<template>
  <div class="carousel-toolbar-item">
    <slot />
    <div
      role="tooltip"
      class="carousel-toolbar-item__tooltip"
      :class="`carousel-toolbar-item__tooltip--${placement}`"
    >
      <p class="carousel-toolbar-item__label">
        {{ label }}
      </p>
      <p
        v-if="hint"
        class="carousel-toolbar-item__hint"
      >
        {{ hint }}
      </p>
    </div>
  </div>
</template>
