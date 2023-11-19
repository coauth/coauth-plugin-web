<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { useRoute } from 'vue-router';
import TheWelcome from '../components/TheWelcome.vue'
import axios from 'axios';
import SpinnerOverlay from '../components/utils/SpinnerOverlay.vue';
import TotpVerifyModule from '../components/totp/TotpVerifyModule.vue';
import TotpRegisterModule from '../components/totp/TotpRegisterModule.vue';
import ReconfirmVerifyModule from '../components/reconfirm/ReconfirmVerifyModule.vue';
import ErrorOverlay from '../components/utils/ErrorOverlay.vue';

const componentName = ref('initial-loader');
const route = useRoute()
const code = ref('');
const validateLoadRequest = async (code: string, action: string) => {
  const payload = {
    code
  }
  try {
    const response = await axios.post(action == 'verify' ? `/api/coauth/verify/load` : `/api/coauth/register/load`, payload);
    if (response.status === 200) {
      if(action == 'verify'){
      const lowercaseModulename= response.data.data.currentModule.toLowerCase();
      componentName.value = action + '-' + lowercaseModulename
      }else{
        const lowercaseModulename= response.data.data.module.toLowerCase();
        componentName.value = action + '-' + lowercaseModulename
      }
    } else {
      componentName.value = 'invalid-request';
    }
    console.log(response);
  } catch (error) {
    componentName.value = 'invalid-request';
    console.log(error);
  }
}


onMounted(() => {
  validateLoadRequest(route.params.code as string, route.params.action as string);
  code.value = route.params.code as string;
})
</script>
<template>
  <SpinnerOverlay  v-if="componentName == 'initial-loader'"/>
  <ErrorOverlay v-if="componentName == 'invalid-request'"  :msg="'Invalid request'" class="p-4"/>
  <TotpVerifyModule v-if="componentName == 'verify-totp'" :code="code" />
  <TotpRegisterModule v-if="componentName == 'register-totp'" :code="code" />
  <ReconfirmVerifyModule v-if="componentName == 'verify-reconfirm'" :code="code" />

</template>
