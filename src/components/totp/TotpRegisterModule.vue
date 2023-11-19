<script setup lang="ts">
import { onMounted, ref } from 'vue';
import SpinnerOverlay from '../utils/SpinnerOverlay.vue';
import IconSpinner from '../icons/IconSpinner.vue';
import SuccessMessage from '../utils/SuccessMessage.vue';
import axios from 'axios';
import ErrorOverlay from '../utils/ErrorOverlay.vue';

const props=defineProps<{
  code: string
}>()
const isSubmitLoading = ref(false);
const moduleLoading=ref(true);
const showModule=ref(false);
const showSuccess=ref(false);
const isModuleError=ref(false);
const totpCode=ref('')
const submitErrorMessage=ref('');
const imageData=ref('');

const loadPage = async (code: string) => {
  const payload = {
    appCode:code
  }
  try {
    const response = await axios.post(`/api/coauth/module/totp/register/view`, payload);
    if (response.status === 200) {
      moduleLoading.value=false;
      showModule.value=true;
      console.log(response);
      imageData.value=response.data.data;
      } else {
      isModuleError.value=true;
    }
  } catch (error) {
    isModuleError.value=true;
    moduleLoading.value=false;
    console.log(error);
  }
}

const verifyRequest=async () => {
  submitErrorMessage.value='';
  isSubmitLoading.value=true;
  if(totpCode.value.length!=6){
    submitErrorMessage.value='TOTP code should be exactly 6 digits';
    isSubmitLoading.value=false;
    totpCode.value='';
    return;
  }
  const payload = {
    appCode:props.code,
    totpCode: totpCode.value
  }
  try {
    totpCode.value='';
    const response = await axios.post(`/api/coauth/module/totp/register/authenticate`, payload);
    if (response.status === 200) {
      showModule.value=false;
      showSuccess.value=true;
    } else {
      submitErrorMessage.value=response.data.error.message;
    }
    isSubmitLoading.value=false;
    showModule.value=false;
    showSuccess.value=true;
  } catch (error:any) {
    isModuleError.value=true;
    if (error.response && error.response.status === 400) {
      submitErrorMessage.value = error.response.data.error.message;
    }else {
      submitErrorMessage.value = 'An unexpected error occurred.';
    }

    console.log(error);
    isSubmitLoading.value=false;
  }
}

onMounted(() => {
  loadPage(props.code);
})

</script>

<template>
  <SpinnerOverlay v-if="moduleLoading"/>
  <SuccessMessage :msg="'Verification successful. Please wait processing transaction...'" class="px-4" v-if="showSuccess"/>
  <ErrorOverlay v-if="isModuleError"  :msg="'Invalid request'" class="p-4"/>
  <div class="w-full " v-if="showModule">
    <div class="flex flex-col justify-center items-center mx-4">
      <div>
        <h1 class="w-full text-xl font-bold leading-tight tracking-tight text-gray-900 md:text-2xl dark:text-white">
          Enable two-factor Authentication
        </h1>
      </div>
      <p class="w-full text-gray-700 dark:text-white my-4">Note: You need to scan and enter the generated code from the
        authenticator app</p>
      <SuccessMessage :msg="'Scan the below QR in an authenticator app like Google, Microsoft Authenticator'" />

      <div class="w-1/2">
        <img :src="imageData" />
      </div>
      <div class="w-full">
        <label for="email" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">Enter verification
          code</label>
        <input v-model="totpCode" name="totpCode" id="totpCode"
          class="bg-gray-50 border border-gray-300 text-gray-900 sm:text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
          placeholder="" required="true">
          <p class="mt-2 text-sm text-red-600 dark:text-red-500  mb-6" v-if="submitErrorMessage!=''"><span class="font-medium">{{ submitErrorMessage }}</span></p>
        <p id="helper-text-explanation" class="mt-2 text-sm text-gray-500 dark:text-gray-400 mb-6">Note: The authenticator
          code in your app resets every 30 seconds</p>
      </div>

      <button @click="verifyRequest" type="button"
        class="w-full text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800 inline-flex items-center justify-center">
        <IconSpinner v-if="isSubmitLoading" />
        Confirm
      </button>
    </div>
  </div>
</template>