<template>
  <div>
    <VerifCamera
      v-if="isShowCamera"
      @closeCamera="isShowCamera = false"
      @setUserImage="pasFoto = $event;"
    />
    <AppContainer>
      <VerifPhotoInput
        :icon="cameraIcon"
        field-name="Foto selfie dengan KTP"
        input-label="Foto Selfie + KTP"
        mode="camera"
        v-model="pasFoto"
        @openCamera="isShowCamera = true"
      />
      <br>
      <br>
      <VerifPhotoInput
        :icon="ktpIcon"
        field-name="Foto KTP"
        input-label="Foto KTP"
        v-model="fotoKTP"
        @input="handleInput"
      />
      <br>
      <br>
      <AppLogs :logs="logs"/>
    </AppContainer>
  </div>
</template>

<script>
  import AppContainer from "@/components/AppContainer";
  import VerifCamera from "@/components/photo/VerifCamera";
  import VerifPhotoInput from "@/components/photo/VerifPhotoInput";
  import AppLogs from "@/components/AppLogs";

  export default {
    name: "App",
    components: {
      AppContainer,
      VerifCamera,
      VerifPhotoInput,
      AppLogs
    },
    data() {
      return {
        isShowCamera: false,
        cameraIcon: require("@/components/photo/camera.svg"),
        ktpIcon: require("@/components/photo/ktp.svg"),
        pasFoto: null,
        fotoKTP: null,
        logs: []
      };
    },
    methods: {
      handleInput($event) {
        this.logs.push(`image set to: ${$event.name}`);
      }
    }
  };
</script>

/**
  matiin validasi size maks
*/