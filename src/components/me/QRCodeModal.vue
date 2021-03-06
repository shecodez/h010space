<template>
  <Modal :isOpen="isOpen" @close="$emit('close')" css="w-11/12 md:max-w-lg mx-auto rounded shadow-lg">
    <div class="bg-base-100 p-4 rounded flex flex-col items-center justify-center">
      <h1>{{ user.name }}#{{ user.pin }}</h1>
      <div class="border rounded m-4">
        <div ref="qrCode" id="qr-code"></div>
      </div>
      <!-- <input v-model="state.options.data" placeholder="Add data" class="input input-bordered" /> -->
      <div class="flex items-center">
        <select v-model="state.extension" class="select select-bordered rounded-r-none">
          <option value="svg">SVG</option>
          <option value="png">PNG</option>
          <option value="jpeg">JPEG</option>
          <option value="webp">WEBP</option>
        </select>
        <button class="btn btn-primary rounded-l-none" @click="download">Download</button>
      </div>
    </div>
  </Modal>
</template>

<script setup lang="ts">
import { onMounted, PropType, reactive, ref, toRefs } from 'vue';
import { Icon } from '@iconify/vue';
import QRCodeStyling, {
  DrawType,
  TypeNumber,
  Mode,
  ErrorCorrectionLevel,
  DotType,
  Gradient,
  CornerSquareType,
  CornerDotType,
  FileExtension,
} from 'qr-code-styling';

import Modal from '@/components/DockUI/Modal.vue';
import { IUser } from '@/data/interfaces';

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false,
  },
  user: {
    type: Object as PropType<IUser>,
    default: {},
  },
});

const { user } = toRefs(props);

const options = {
  width: 300,
  height: 300,
  type: 'svg' as DrawType,
  data: `https://h010space.com/h010dex/${user.value.name}#${user.value.pin}`,
  image: '/logo.svg',
  margin: 10,
  qrOptions: {
    typeNumber: 0 as TypeNumber,
    mode: 'Byte' as Mode,
    errorCorrectionLevel: 'Q' as ErrorCorrectionLevel,
  },
  imageOptions: {
    hideBackgroundDots: true,
    imageSize: 0.5,
    margin: 5,
    crossOrigin: 'anonymous',
  },
  dotsOptions: {
    //color: '#4506cb',
    gradient: {
      type: 'radial', // 'linear',
      rotation: 0,
      colorStops: [
        { offset: 0, color: '#22d3ee' },
        { offset: 1, color: '#4F46E5' },
      ],
    } as Gradient,
    type: 'extra-rounded' as DotType,
  },
  backgroundOptions: {
    color: '#ffffff',
    // gradient: {
    //   type: 'linear', // 'radial'
    //   rotation: 0,
    //   colorStops: [{ offset: 0, color: '#ededff' }, { offset: 1, color: '#e6e7ff' }]
    // },
  },
  cornersSquareOptions: {
    color: '#3d4451',
    type: 'extra-rounded' as CornerSquareType,
    // gradient: {
    //   type: 'linear', // 'radial'
    //   rotation: 180,
    //   colorStops: [{ offset: 0, color: '#25456e' }, { offset: 1, color: '#4267b2' }]
    // },
  },
  cornersDotOptions: {
    color: '#4F46E5',
    type: 'dot' as CornerDotType,
    // gradient: {
    //   type: 'linear', // 'radial'
    //   rotation: 180,
    //   colorStops: [{ offset: 0, color: '#00266e' }, { offset: 1, color: '#4060b3' }]
    // },
  },
};

const state = reactive({
  options,
  extension: 'svg',
  qrCode: new QRCodeStyling(options),
});

const qrCode = ref<HTMLElement>();

onMounted(() => {
  state.qrCode.append(qrCode.value);
});

function download() {
  state.qrCode.download({ extension: state.extension as FileExtension });
}
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
