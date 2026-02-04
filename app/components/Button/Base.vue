<script setup lang="ts">
const props = defineProps<{
  disabled?: boolean
  /**
   * type should never be used, because this will always be a button.
   *
   * If you want a link use `TagLink` instead.
   *  */
  type?: never
}>()

const el = ref<HTMLButtonElement | null>(null)

defineExpose({
  focus: () => el.value?.focus(),
})
</script>

<template>
  <button
    ref="el"
    class="btn"
    :class="{
      'opacity-50 cursor-not-allowed': disabled,
    }"
    type="button"
    :disabled="disabled ? true : undefined"
  >
    <slot />
  </button>
</template>
