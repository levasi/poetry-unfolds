<script setup lang="ts">
import { Icon } from '@iconify/vue'
import { useFavorites } from '~/composables/useFavorites'

const { t } = useI18n()
const { toggle, isFavorite } = useFavorites()

const props = withDefaults(
  defineProps<{
    title: string
    slug: string
    poemId?: string
    showCarousel?: boolean
    instagramSize?: 'xs' | 'sm' | 'md' | 'lg'
    variant?: 'pdp' | 'banner'
  }>(),
  { showCarousel: true, instagramSize: 'sm', variant: 'pdp' },
)

const liked = computed(() => (props.poemId ? isFavorite(props.poemId) : false))
const showActions = computed(() => Boolean(props.poemId || props.showCarousel))
const copied = ref(false)

async function sharePoem() {
  if (!import.meta.client) return
  const url = window.location.href
  if (navigator.share) {
    try {
      await navigator.share({ title: props.title, url })
      return
    } catch {
      // cancelled — fall through
    }
  }
  await navigator.clipboard.writeText(url)
  copied.value = true
  window.setTimeout(() => {
    copied.value = false
  }, 2000)
}
</script>

<template>
  <div
    v-if="showActions"
    class="poem-title__actions"
    :class="`poem-title__actions--${variant}`"
  >
    <button
      v-if="poemId"
      type="button"
      class="poem-title__action"
      :class="{ 'poem-title__action--liked': liked }"
      :aria-label="liked ? t('card.favoriteRemove') : t('card.favoriteAdd')"
      @click.prevent="poemId && toggle(poemId)"
    >
      <Icon
        :icon="liked ? 'heroicons:heart-solid' : 'heroicons:heart'"
        class="poem-title__action-icon"
        aria-hidden="true"
      />
    </button>
    <button
      v-if="poemId"
      type="button"
      class="poem-title__action"
      :aria-label="copied ? t('viewer.linkCopied') : t('viewer.sharePoem')"
      @click.prevent="sharePoem"
    >
      <Icon
        icon="heroicons:share"
        class="poem-title__action-icon"
        aria-hidden="true"
      />
    </button>
    <PoemCarouselIcon
      v-if="showCarousel"
      :slug="slug"
      :size="instagramSize"
    />
  </div>
</template>
