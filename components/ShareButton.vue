<script setup lang="ts">
import { computed } from 'vue'

import type { LightningReceipt } from '~/types/index'

import { Icon } from '@iconify/vue'
import { useShare, useClipboard } from '@vueuse/core'

import { Button } from '~/components/ui/button'

const props = defineProps<{
  form: LightningReceipt
}>()

const { share, isSupported: isShareSupported } = useShare()
const { copy, isSupported: isClipboardSupported } = useClipboard()

const link = computed(() => {
  const url = new URL(window.location.href)

  url.searchParams.set('invoice', props.form.invoice)
  url.searchParams.set('preimage', props.form.preimage)

  return url.toString()
})

const isDisabled = computed(
  () => !(props.form.invoice === '' || props.form.preimage === ''),
)

function startShare() {
  share({
    title: 'Lightning Receipt',
    text: `Your invoice is paid. Here is the receipt:`,
    url: link.value,
  })
}

function copyLink() {
  copy(link.value)
}
</script>

<template>
  <div class="flex gap-2">
    <!-- Native Share Button (if supported) -->
    <Button 
      v-if="isShareSupported && isDisabled" 
      @click.prevent="startShare" 
      variant="secondary"
    >
      <Icon icon="lucide:share-2" /> Share
    </Button>
    
    <!-- Copy Link Button (always available) -->
    <Button 
      v-if="isClipboardSupported && isDisabled" 
      @click.prevent="copyLink" 
      variant="outline"
    >
      <Icon icon="lucide:copy" /> Copy Link
    </Button>
  </div>
</template>