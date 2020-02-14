<template lang="html">
  <q-input
    id="image"
    v-model="capturedImage"
    type="file"
    name="image"
    label="Choose a Picture Manually"
    accept="image/*;capture=camera"
    capture="environment"
    standout="bg-info"
    input-class="inputfile"
    rounded
    @change="captureImage"
  />
</template>

<script>
import { scan } from "../misc/scanner.js";
import { imageDataFromFile } from "../misc/image-data.js";
import CommonAPI from "../mixins/CommonAPI.vue";
import Worker from "../worker/jsqr.js";

export default {
  name: "qrcode-capture",

  mixins: [CommonAPI],

  props: {
    worker: {
      type: Function,
      default: Worker
    }
  },
  data() {
    return {
      capturedImage: null,
    };
  },
  methods: {
    onChangeInput(event) {
      const files = [...event.target.files];
      const resultPromises = files.map(this.processFile);

      resultPromises.forEach(this.onDetect);
    },

    async processFile(file) {
      const imageData = await imageDataFromFile(file);
      const scanResult = await scan(this.worker, imageData);

      return scanResult;
    }
  }
};
</script>
