<script setup lang="ts">
/**
 * Poem title + optional Instagram / carousel shortcut. Use `pdp` on poem detail, `banner` on home hero.
 * Set `instagramSize` to override the default icon scale.
 */
const props = withDefaults(
  defineProps<{
    title: string
    slug: string
    variant: 'pdp' | 'banner'
    /** Set false to hide the Instagram carousel link */
    showCarousel?: boolean
    /** Instagram / carousel shortcut icon size (default `sm` — same as PoetryCard). */
    instagramSize?: 'xs' | 'sm' | 'md' | 'lg'
    /** When set, shows favorite toggle (same behavior as PoetryCard). */
    poemId?: string
    /** Hide inline actions when the reader renders them beside the author instead. */
    showActions?: boolean
  }>(),
  { showCarousel: true, instagramSize: 'sm', showActions: true },
)

const heading = computed(() => (props.variant === 'pdp' ? 'h1' : 'h3'))
</script>

<template>
  <div
    class="poem-title"
    :class="[
      variant === 'pdp' ? 'poem-title--pdp' : 'poem-title--banner',
    ]"
  >
    <component
      :is="heading"
      class="poem-title__heading"
    >
      {{ title }}
    </component>
    <PoemTitleActions
      v-if="showActions"
      :title="title"
      :slug="slug"
      :variant="variant"
      :poem-id="poemId"
      :show-carousel="showCarousel"
      :instagram-size="instagramSize"
    />
  </div>
</template>
