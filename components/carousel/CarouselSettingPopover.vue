<script setup lang="ts">
import { Icon } from '@iconify/vue'

const props = withDefaults(
  defineProps<{
    id: string
    icon: string
    label: string
    open: boolean
    /** Panel grows toward the preview (right) by default. */
    placement?: 'left' | 'right'
    panelTarget?: string
  }>(),
  { placement: 'right', panelTarget: '#carousel-settings-disclosure' },
)

const emit = defineEmits<{
  toggle: [id: string]
}>()

const canTeleport = ref(false)

onMounted(() => {
  canTeleport.value = true
})

function onTriggerClick() {
  emit('toggle', props.id)
}
</script>

<template>
  <div
    class="carousel-setting"
    :data-setting-id="id"
  >
    <button
      type="button"
      class="ds-icon-btn carousel-setting__trigger"
      :class="{ 'carousel-setting__trigger--active': open }"
      :aria-expanded="open"
      :aria-controls="`carousel-setting-panel-${id}`"
      :aria-label="label"
      :title="label"
      @click.stop="onTriggerClick"
    >
      <Icon :icon="icon" class="carousel-page__icon" aria-hidden="true" />
    </button>

    <Teleport v-if="canTeleport" :to="panelTarget">
      <div
        v-show="open"
        :id="`carousel-setting-panel-${id}`"
        class="carousel-setting__panel"
        :class="`carousel-setting__panel--${placement}`"
        :aria-label="label"
        @click.stop
      >
        <p class="carousel-setting__title">{{ label }}</p>
        <div class="carousel-setting__body">
          <slot />
        </div>
      </div>
    </Teleport>
  </div>
</template>
