<template>
  <div class="verif-photo">
    <div class="verif-photo__selfie">
      <verif-photo-input
        :icon="cameraIcon"
        :icon-error="cameraErrorIcon"
        :validation="$v.pasFoto"
        field-name="Foto selfie dengan KTP"
        input-label="Foto Selfie + KTP"
        mode="camera"
        v-model="pasFoto"
      />
    </div>

    <div class="verif-photo__ktp">
      <verif-photo-input
        :icon="ktpIcon"
        :icon-error="ktpErrorIcon"
        :validation="$v.fotoKTP"
        field-name="Foto KTP"
        input-label="Foto KTP"
        v-model="fotoKTP"
      />
    </div>

    <div class="verif-photo__npwp">
      <verif-photo-input
        :icon="npwpIcon"
        :icon-error="npwpErrorIcon"
        :validation="$v.fotoNPWP"
        field-name="Foto NPWP"
        input-label="Foto NPWP"
        v-model="fotoNPWP"
      />
    </div>

    <div class="verif-photo__npwp-number">
      <input-group
        :is-readonly="isUploading"
        :validation="$v.noNPWP"
        field-name="Nomor NPWP"
        input-id="lender-v4-verification__npwp"
        input-label="Nomor NPWP"
        input-type="text"
        v-model="noNPWP"
      />
    </div>

    <div class="verif-photo__btn">
      <verif-btn
        :is-disabled="isUploading"
        @click="upload"
        btn-label="Upload"
        btn-type="upload"
      />
    </div>
  </div>
</template>

<script>
  import VerifBtn from "../VerifBtn";
  import VerifPhotoInput from "./VerifPhotoInput";
  import InputGroup from "@/components/__ui/input/InputGroup";

  import { required, numeric } from "vuelidate/lib/validators";
  import { validationMixin } from "vuelidate";
  import { exactLength } from "@/__misc/validators";

  export default {
    name: "VerifPhoto",
    components: {
      VerifBtn,
      InputGroup,
      VerifPhotoInput
    },
    mixins: [validationMixin],
    validations: {
      fotoKTP: { required },
      pasFoto: { required },
      fotoNPWP: { required },
      noNPWP: { required, numeric, exactLength: exactLength(15) }
    },
    data() {
      return {
        isShowCameraModal: false,
        fotoKTP: null,
        fotoNPWP: null,
        noNPWP: "",
        isUploading: false,
        cameraIcon: require("./camera.svg"),
        cameraErrorIcon: require("./camera-error.svg"),
        ktpIcon: require("./ktp.svg"),
        ktpErrorIcon: require("./ktp-error.svg"),
        npwpIcon: require("./npwp.svg"),
        npwpErrorIcon: require("./npwp-error.svg")
      };
    },
    computed: {
      pasFoto: {
        get() {
          return this.$store.state.verification.userImage;
        },
        set(value) {
          this.$store.commit("verification/setUserImage", value);
        }
      }
    },
    methods: {
      handleOpenCamera() {
        this.isShowCameraModal = true;
      },
      upload() {
        this.$v.$touch();

        if (!this.$v.$invalid) {
          this.isUploading = true;

          this.$accountService
            .checkKtp({
              fotoKTP: this.fotoKTP,
              pasFoto: this.pasFoto,
              jenisLender: "Individu",
              fotoNPWP: this.fotoNPWP,
              noNPWP: this.noNPWP
            })
            .then(res => {
              this.$emit("success");

              this.$swal({
                type: "success",
                title: "Verifikasi Berhasil",
                text: "Wahaaw. Foto kamu kini telah terverifikasi!"
              });
            })
            .finally(() => {
              this.isUploading = false;
            });
        }
      }
    }
  };
</script>

<style lang="scss" scoped>
  .verif-photo {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-template-rows: repeat(4, auto);
    grid-column-gap: 20px;
    grid-row-gap: 20px;

    &__selfie {
      grid-area: 1 / 1 / 2 / 3;
    }

    &__ktp {
      grid-area: 2 / 1 / 3 / 2;
    }

    &__npwp {
      grid-area: 2 / 2 / 3 / 3;
    }

    &__npwp-number {
      grid-area: 3 / 1 / 4 / 3;
    }

    &__btn {
      grid-area: 4 / 1 / 5 / 3;
    }

    @media (max-width: 425px) {
      display: grid;
      grid-template-columns: 1fr;
      grid-template-rows: initial;
      grid-column-gap: initial;
      grid-row-gap: 20px;

      &__selfie {
        grid-area: initial;
      }

      &__ktp {
        grid-area: initial;
      }

      &__npwp {
        grid-area: initial;
      }

      &__npwp-number {
        grid-area: initial;
      }

      &__btn {
        grid-area: initial;
      }
    }
  }
</style>