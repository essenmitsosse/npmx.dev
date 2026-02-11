<script setup lang="ts">
import Readme from '../Readme.vue'

const props = defineProps<{
  packageName: string
  requestedVersion: string | null
  repositoryUrl: string | null
}>()

// Fetch README for specific version if requested, otherwise latest
const { data } = useLazyFetch<ReadmeResponse>(
  () => {
    const base = `/api/registry/readme/${props.packageName}`
    const version = props.requestedVersion
    return version ? `${base}/v/${version}` : base
  },
  { default: () => ({ html: '', md: '', playgroundLinks: [], toc: [] }) },
)

// Track active TOC item based on scroll position
const tocItems = computed(() => data.value?.toc ?? [])
const { activeId: activeTocId } = useActiveTocItem(tocItems)

//copy README file as Markdown
const { copied: copiedReadme, copy: copyReadme } = useClipboard({
  source: () => data.value?.md ?? '',
  copiedDuring: 2000,
})
</script>

<template>
  <div class="flex flex-wrap items-center justify-between mb-3 px-1">
    <h2 id="readme-heading" class="group text-xs text-fg-subtle uppercase tracking-wider">
      <LinkBase to="#readme">
        {{ $t('package.readme.title') }}
      </LinkBase>
    </h2>
    <div class="flex gap-2">
      <!-- Copy readme as Markdown button -->
      <TooltipApp v-if="data?.md" :text="$t('package.readme.copy_as_markdown')" position="bottom">
        <ButtonBase
          @click="copyReadme()"
          :aria-pressed="copiedReadme"
          :aria-label="copiedReadme ? $t('common.copied') : $t('package.readme.copy_as_markdown')"
          :classicon="copiedReadme ? 'i-carbon:checkmark' : 'i-simple-icons:markdown'"
        >
          {{ copiedReadme ? $t('common.copied') : $t('common.copy') }}
        </ButtonBase>
      </TooltipApp>
      <ReadmeTocDropdown
        v-if="data?.toc && data.toc.length > 1"
        :toc="data.toc"
        :active-id="activeTocId"
      />
    </div>
  </div>

  <!-- eslint-disable vue/no-v-html -- HTML is sanitized server-side -->
  <Readme v-if="data?.html" :html="data.html" />
  <p v-else class="text-fg-muted italic">
    {{ $t('package.readme.no_readme') }}
    <a
      v-if="repositoryUrl"
      :href="repositoryUrl"
      target="_blank"
      rel="noopener noreferrer"
      class="link text-fg underline underline-offset-4 decoration-fg-subtle hover:(decoration-fg text-fg) transition-colors duration-200"
      >{{ $t('package.readme.view_on_github') }}</a
    >
  </p>
</template>

<style module>
.areaReadme {
  grid-area: readme;
}

.areaReadme > :global(.readme) {
  overflow-x: hidden;
}
</style>
