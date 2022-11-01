<template>
  <q-page padding>
    <p class="text-h6">Camera</p>

    <div class="row">
      <div class="col-12 text-center">
        <video autoplay width="250rem" ref="vidioplay" />
      </div>

      <div class="col-12 text-center q-mt-md">
        <q-btn
          v-if="!cameraStart"
          label="Access Camera"
          color="primary"
          icon="camera"
          @click="useCamera"
        ></q-btn>

        <q-btn
          v-else
          label="Take Photo"
          color="primary"
          icon="camera"
          @click="takePhoto"
        ></q-btn>
      </div>

      <div class="col-12 text-center q-mt-md">
        <img src="" ref="imgTakePhoto" width="250rem" />
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",
  data() {
    return {
      enableCamera: false,
      cameraStart: false,
      imageCapture: null,
      track: null,
    };
  },
  mounted() {
    if (navigator.mediaDevices.getUserMedia) {
      this.enableCamera = true;
    }
  },
  methods: {
    useCamera() {
      navigator.mediaDevices
        .getUserMedia({ video: true })
        .then((mediaStream) => {
          this.cameraStart = true;
          this.$refs.vidioplay.srcObject = mediaStream;
          this.track = mediaStream.getVideoTracks()[0];
          this.imageCapture = new ImageCapture(this.track);
        });
    },
    takePhoto() {
      this.imageCapture
        .takePhoto()
        .then((blob) => {
          createImageBitmap(blob);
          const reader = new FileReader();
          reader.readAsDataURL(blob);
          reader.onloadend = () => {
            this.$refs.imgTakePhoto.src = reader.result;
          };
        })
        .catch((error) => console.log(error));
    },
  },
};
</script>
