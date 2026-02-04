<script setup lang="ts">
import type { NuxtLinkProps } from '#app'

const props = defineProps<
  {
    /** Disabled links will be displayed as plain text */
    disabled?: boolean
    /**
     * `type` should never be used, because this will always be a link.
     *
     * If you want a button use `TagButton` instead.
     * */
    type?: never
  } &
    /** This makes sure the link always has either `to` or `href` */
    (Required<Pick<NuxtLinkProps, 'to'>> | Required<Pick<NuxtLinkProps, 'href'>>) &
    NuxtLinkProps
>()
</script>

<template>
  <!-- This is only a placeholder implementation yet. It will probably need some additional styling, but note: A disabled link is just text. -->
  <span v-if="disabled" class="opacity-50"><slot /></span>
  <NuxtLink
    v-else
    class="btn"
    :class="
      {
        'opacity-50 cursor-not-allowed': disabled,
      }
    "
  >
    <slot />
  </NuxtLink>
</template>
