<script setup lang="ts">
import { onMounted, ref } from 'vue';
import SpinnerOverlay from '../utils/SpinnerOverlay.vue';
import IconSpinner from '../icons/IconSpinner.vue';
import SuccessMessage from '../utils/SuccessMessage.vue';
import axios, { AxiosError } from 'axios';
import ErrorOverlay from '../utils/ErrorOverlay.vue';

const props = defineProps<{
  code: string
}>()
const isSubmitLoading = ref(false);
const moduleLoading = ref(true);
const showModule = ref(false);
const showSuccess = ref(false);
const isModuleError = ref(false);
const inputValue = ref('')
const submitErrorMessage = ref('');
const actualValue = ref('');

const loadPage = async (code: string) => {
  const payload = {
    appCode: code
  }
  try {
    const response = await axios.post(`/api/coauth/module/reconfirm/verification/view`, payload);
    if (response.status === 200) {
      moduleLoading.value = false;
      showModule.value = true;
      actualValue.value = response.data.data;
    } else {
      isModuleError.value = true;
    }
  } catch (error) {
    isModuleError.value = true;
    moduleLoading.value = false;
    console.log(error);
  }
}

const verifyRequest = async () => {
  submitErrorMessage.value = '';
  isSubmitLoading.value = true;
  if (inputValue.value.length == 0) {
    submitErrorMessage.value = 'Input value cannot be empty';
    isSubmitLoading.value = false;
    inputValue.value = '';
    return;
  }
  const payload = {
    appCode: props.code,
    reconfirmInputText: inputValue.value
  }
  try {
    inputValue.value = '';
    const response = await axios.post(`/api/coauth/module/reconfirm/verification/authenticate`, payload);
    if (response.status === 200) {
      showModule.value = false;
      showSuccess.value = true;
      callParentTransactionComplete();
    } else {
      submitErrorMessage.value = response.data.error.message;
    }
    isSubmitLoading.value = false;
    showModule.value = false;
    showSuccess.value = true;
  } catch (error: any) {
    if (error.response && error.response.status === 400) {
      submitErrorMessage.value = error.response.data.error.message;
    } else {
      submitErrorMessage.value = 'An unexpected error occurred.';
    }

    console.log(error);
    isSubmitLoading.value = false;
  }
}

const callParentTransactionComplete = () => {

  if (window.parent) {
    window.parent.postMessage('transactionComplete', '*');
  }
}

onMounted(() => {
  loadPage(props.code);
})
</script>

<template>
  <SpinnerOverlay v-if="moduleLoading" />
  <SuccessMessage :msg="'Verification successful. Please wait processing transaction...'" class="px-4"
    v-if="showSuccess" />
  <ErrorOverlay v-if="isModuleError" :msg="'Invalid request'" class="p-4" />
  <div class="w-full " v-if="showModule">
    <div class="flex flex-col justify-center items-center mx-4">
      <div>
        <h5 class="w-full text-xl font-bold dark:text-white">Please authorize this Transaction</h5>
      </div>
      <div class="mt-4 dark:border-cyan-300 w-full mb-8">
        <label for="email" class="block mb-2 text-sm font-normal text-gray-900 dark:text-white"
          v-html="actualValue"></label>
        <input type="email" name="email" id="email" v-model="inputValue"
          class="bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
          placeholder="" required="true">
          <p class="mt-2 text-sm text-red-600 dark:text-red-500  mb-6" v-if="submitErrorMessage!=''"><span class="font-medium">{{ submitErrorMessage }}</span></p>
      </div>
      <button type="button" @click="verifyRequest"
        class="w-full text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800 inline-flex items-center justify-center">
        <IconSpinner v-if="isSubmitLoading" />
        Submit
      </button>
    </div>
  </div>
</template>

