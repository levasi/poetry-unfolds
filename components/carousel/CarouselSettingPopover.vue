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
  }>(),
  { placement: 'right' },
)

const emit = defineEmits<{
  toggle: [id: string]
}>()

const rootRef = ref<HTMLElement | null>(null)
const panelStyle = ref<Record<string, string>>({})

defineExpose({ rootRef })

function onTriggerClick() {
  emit('toggle', props.id)
}

function isCompactSettingsViewport() {
  return window.matchMedia('(max-width: 1023px)').matches
}

/** Pin the panel under the settings toolbar so it never shifts with the icon. */
function placePanelUnderToolbar() {
  const root = rootRef.value
  if (!root || !isCompactSettingsViewport()) {
    panelStyle.value = {}
    return
  }

  const toolbar = root.closest('.carousel-page__preview-toolbar--settings') as HTMLElement | null
  if (!toolbar) {
    panelStyle.value = {}
    return
  }

  const rect = toolbar.getBoundingClientRect()
  const margin = 8
  const left = Math.max(margin, rect.left)
  const rightEdge = Math.min(window.innerWidth - margin, rect.right)
  const width = Math.max(0, rightEdge - left)

  panelStyle.value = {
    position: 'fixed',
    top: `${rect.bottom + 8}px`,
    left: `${left}px`,
    width: `${width}px`,
    maxWidth: 'none',
    right: 'auto',
  }
}

watch(
  () => props.open,
  (isOpen) => {
    if (!isOpen) {
      // Keep last geometry until next open so close is not a left-reset jump.
      return
    }
    placePanelUnderToolbar()
  },
  { flush: 'sync' },
)

function onViewportChange() {
  if (props.open) placePanelUnderToolbar()
}

onMounted(() => {
  window.addEventListener('resize', onViewportChange)
  window.addEventListener('orientationchange', onViewportChange)
  window.addEventListener('scroll', onViewportChange, true)
})

onUnmounted(() => {
  window.removeEventListener('resize', onViewportChange)
  window.removeEventListener('orientationchange', onViewportChange)
  window.removeEventListener('scroll', onViewportChange, true)
})
</script>

<template>
  <div
    ref="rootRef"
    class="carousel-setting"
    :class="{ 'carousel-setting--open': open }"
    :data-setting-id="id"
  >
    <button
      type="button"
      class="ds-icon-btn carousel-setting__trigger"
      :class="{ 'carousel-setting__trigger--active': open }"
      :aria-expanded="open"
      aria-haspopup="dialog"
      :aria-controls="`carousel-setting-panel-${id}`"
      :aria-label="label"
      :title="label"
      @click.stop="onTriggerClick"
    >
      <Icon :icon="icon" class="carousel-page__icon" aria-hidden="true" />
    </button>

    <div
      v-show="open"
      :id="`carousel-setting-panel-${id}`"
      class="carousel-setting__panel"
      :class="`carousel-setting__panel--${placement}`"
      :style="panelStyle"
      role="dialog"
      :aria-label="label"
      @click.stop
    >
      <p class="carousel-setting__title">{{ label }}</p>
      <div class="carousel-setting__body">
        <slot />
      </div>
    </div>
  </div>
</template>
