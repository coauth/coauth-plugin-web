<script setup lang="ts">
import { ref } from 'vue';
import SpinnerOverlay from '../utils/SpinnerOverlay.vue';
import IconSpinner from '../icons/IconSpinner.vue';
import SuccessMessage from '../utils/SuccessMessage.vue';

defineProps<{
  code: string
}>()

const actualValue = ref('blah blah');

const isLoading = ref(false);

const inputValue = ref('');
</script>

<template>
  <SpinnerOverlay />
  <SuccessMessage :msg="'Verification successful. Please wait processing transaction...'" class="px-4" />
  <div class="w-full ">
    <div class="flex flex-col justify-center items-center mx-4">
      <div>
        <h5 class="w-full text-xl font-bold dark:text-white">Please authorize this Transaction</h5>
      </div>
      <div class="mt-4 dark:border-cyan-300 w-full">
        <label for="email" class="block mb-2 text-sm font-normal text-gray-900 dark:text-white">Re-type the word:
          <span class="text-xl font-bold">{{ actualValue }}</span> in the input box below</label>
        <input type="email" name="email" id="email" v-model="inputValue"
          class="bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
          placeholder="" required="true">
        <p class="mt-2 text-sm text-red-600 dark:text-red-500  mb-6"><span class="font-medium">Entered
            value does not match</span></p>
      </div>
      <button type="button"
        class="w-full text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800 inline-flex items-center justify-center">
        <IconSpinner v-if="isLoading" />
        Verify PIN
      </button>
    </div>
  </div>
</template>

