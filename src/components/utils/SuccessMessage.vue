<script setup lang="ts">


defineProps<{
  msg: string
}>()
</script>
<template>
<div class="flex w-full align-middle justify-center">
    <div
      class="w-full flex items-center p-4 mb-4 text-sm text-green-800 border border-green-300 rounded-lg bg-green-50 dark:bg-gray-800 dark:text-green-400 dark:border-green-800"
      role="alert">
      <IconInfo />
      <span class="sr-only">Info</span>
      <div>
        <span class="font-medium">{{msg}}</span>
      </div>
    </div>
  </div>
</template>