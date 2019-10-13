<template>
  <div
    @click="closeCamera"
    class="verif-camera"
  >
    <div
      @click.stop
      class="verif-camera__panel"
    >
      <div style="color: #707070; font-size: 16px; margin-bottom: 20px; text-align: center;">Ayo ambil foto selfie sambil memegang KTP. Say Cheese ;)</div>

      <div class="verif-camera__panel__placeholder">
        <!-- <video
          autoplay
          class="verif-camera__panel__placeholder__video"
          height="450"
          ref="video"
          v-show="captureMode"
          width="600"
        ></video>-->

        <video
          autoplay
          class="verif-camera__panel__placeholder__video"
          ref="video"
          v-show="captureMode"
        ></video>

        <!-- <img
          :src="cameraOverlay"
          alt="Camera Overlay"
          class="verif-camera__panel__placeholder__overlay"
          v-if="captureMode"
        >-->

        <img
          :src="image"
          class="verif-camera__panel__placeholder__result"
          v-if="!captureMode"
        >

        <canvas
          class="verif-camera__panel__placeholder__canvas"
          height="450"
          id="canvas"
          ref="canvas"
          width="600"
        ></canvas>
      </div>

      <div style="display: flex; justify-content: center; align-items: center; margin-top: 20px;">
        <button
          @click="capture"
          v-if="captureMode"
        >Ambil Foto</button>

        <button
          @click="reCapture"
          v-if="!captureMode"
        >Ulang</button>

        <button
          @click="saveImage"
          v-if="!captureMode"
        >Simpan</button>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "VerifCamera",
    data() {
      return {
        video: {},
        canvas: {},
        image: null,
        captureMode: true,
        imageFile: null,
        cameraOverlay: require("./camera-overlay.png")
      };
    },
    methods: {
      closeCamera() {
        this.$emit("closeCamera");
      },
      activateCamera() {
        this.video = this.$refs.video;

        if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
          navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
            this.video.srcObject = stream;
            this.video.play();
          });
        }
      },
      deActivateCamera() {
        //
      },
      capture() {
        this.canvas = this.$refs.canvas;

        var context = this.canvas.getContext("2d").drawImage(this.video, 0, 0);

        this.image = canvas.toDataURL("image/jpg");

        this.captureMode = false;
      },
      reCapture() {
        this.image = null;
        this.captureMode = true;
      },
      saveImage() {
        this.imageFile = this.dataURLtoFile(this.image);
        this.$emit("setUserImage", this.imageFile);
        this.closeCamera();
      },
      dataURLtoFile(dataurl) {
        var arr = dataurl.split(","),
          mime = arr[0].match(/:(.*?);/)[1],
          bstr = atob(arr[1]),
          n = bstr.length,
          u8arr = new Uint8Array(n);

        while (n--) {
          u8arr[n] = bstr.charCodeAt(n);
        }

        return new File([u8arr], "foto-diri", { type: mime });
      }
    },
    mounted() {
      if (process.browser) {
        this.activateCamera();
      }
    },
    beforeDestroy() {
      if (process.browser) {
        this.deActivateCamera();
      }
    },
    created() {
      document.body.classList.add("lock");
    },
    destroyed() {
      document.body.classList.remove("lock");
    }
  };
</script>

<style lang="scss" scoped>
  .verif-camera {
    position: fixed;
    background-color: rgba(0, 0, 0, 0.7);
    top: 0;
    left: 0;
    z-index: 99;
    padding: 50px 10px;
    width: 100%;
    height: 100%;
    overflow-y: auto;
    display: flex;
    justify-content: center;
    align-items: center;

    .verif-camera__panel {
      max-width: 800px;
      background-color: #ffffff;
      border-radius: 10px;
      min-height: 300px;
      margin: 0 auto;
      padding: 20px 20px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;

      .verif-camera__panel__placeholder {
        max-width: 600px;
        max-height: 450px;
        position: relative;

        .verif-camera__panel__placeholder__video {
          width: 100%;
          height: 300px;
          // width: 600;
          // height: 450;
          background-color: #eaeaea;
          margin: 0 auto;
          transform: rotateY(180deg);
        }

        .verif-camera__panel__placeholder__overlay {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
        }

        .verif-camera__panel__placeholder__result {
          width: 100%;
        }

        .verif-camera__panel__placeholder__canvas {
          display: none;
          width: 100%;
          height: auto;
        }
      }

      button {
        padding: 10px 20px;
        font-size: 14px;
        background-color: #ffc200;
        border-radius: 5px;
        border: none;
        cursor: pointer;
        margin: 0 5px;

        &:hover {
          background-color: darken(#ffc200, 2.5%);
        }

        &:active,
        &:focus {
          outline: none;
        }
      }
    }

    @media (max-width: 768px) {
      .verif-camera__panel {
        padding: 20px;
      }
    }

    @media (max-width: 375px) {
      .verif-camera__panel {
        padding: 20px 10px;
      }
    }
  }
</style>