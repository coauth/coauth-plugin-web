<script setup lang="ts">
import IconInfo from '../icons/IconInfo.vue';

defineProps<{
  msg: string
}>()
</script>
<template>
  <div class="w-full ">
<div class="flex align-middle justify-center">
  <div class="w-full flex items-center p-4 mb-4 text-sm text-red-800 border border-red-300 rounded-lg bg-red-50 dark:bg-gray-800 dark:text-red-400 dark:border-red-800" role="alert">
      <IconInfo />
      <span class="sr-only">Error</span>
      <div>
        <span class="font-medium">{{msg}}</span>
      </div>
    </div>
  </div>
</div>
</template>