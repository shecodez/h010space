<template>
  <Banner v-if="show" dismissible>
    <div class="alert alert-warning flex-1 border-l-4 border-warning rounded-none">
      <div class="flex-1">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="w-6 h-6 mx-2 stroke-current">
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"
          ></path>
        </svg>
        <label class="mx-3">Please confirm your email address.</label>
      </div>
      <div class="flex-none">
        <button class="btn btn-sm btn-ghost mr-8">Resend</button>
      </div>
    </div>
  </Banner>
</template>

<script setup lang="ts">
//import { Icon } from "@iconify/vue";
import { defineProps, toRefs } from 'vue';

import Banner from '@/components/DockUI/Banner.vue';

const props = defineProps({
  show: {
    type: Boolean,
    default: false,
  },
});

const { show } = toRefs(props);
</script>
