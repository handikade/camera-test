<template>
  <div :class="styleClass">
    <div class="verif-photo-input__label">{{ inputLabel }}</div>

    <input
      @input="onImageSelect"
      accept="image/*"
      ref="fileInput"
      type="file"
    >

    <div
      @click="selectImage"
      class="verif-photo-input__box"
      v-if="!value"
    >
      <img
        :src="activeIcon"
        alt="Icon"
        class="verif-photo-input__box__icon"
      >
    </div>

    <div
      @click="selectImage"
      class="verif-photo-input__placeholder"
      v-if="value"
    >
      <img
        :src="imageUrl"
        alt="Image"
      >
      <div class="verif-photo-input__placeholder__overlay">Ubah</div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "VerifPhotoInput",
    props: {
      value: null,
      inputLabel: { type: String, default: "" },
      fieldName: { type: String, default: "" },
      icon: { type: String, default: null },
      iconError: { type: String, default: null },
      mode: { type: String, default: "" },
      validation: {
        type: Object,
        default() {
          return {};
        }
      }
    },
    data() {
      return {
        imageFile: null,
        validImageTypes: ["image/jpg", "image/jpeg", "image/png"]
      };
    },
    computed: {
      isError() {
        return this.validation.$error;
      },
      styleClass() {
        return [
          "verif-photo-input",
          {
            "verif-photo-input--is-error": this.isError
          }
        ];
      },
      imageUrl() {
        return this.value ? URL.createObjectURL(this.value) : null;
      },
      activeIcon() {
        return this.isError ? this.iconError : this.icon;
      }
    },
    methods: {
      selectImage() {
        if (this.mode == "camera") {
          this.$emit("openCamera");
        } else {
          this.$refs.fileInput.click();
        }
      },
      onImageSelect(e) {
        this.imageFile = e.target.files[0];

        if (this.imageFile) {
          this.validate().then(res => {
            if (res) this.$emit("input", this.imageFile);
          });
        }
      },
      promptErrorAlert(message) {
        alert(message);
      },
      async validate() {
        let validateType = false;
        let validateSize = false;
        let validateDimension = false;

        validateType = this.validateType();
        console.log("validate type", validateType);

        if (validateType) {
          validateSize = this.validateSize();
          console.log("validate size", validateSize);
        }

        if (validateSize) {
          await this.validateDimension().then(res => {
            validateDimension = res;
          });
          console.log("validate dimension", validateDimension);
        }

        console.log("----------------------------------");

        return new Promise((resolve, reject) => {
          resolve(validateDimension && validateType && validateSize);
        });
      },
      validateType() {
        try {
          const fileType = this.imageFile.type;

          console.log("tipe file", fileType);
          console.log("tipe valid?", this.validImageTypes.includes(fileType));

          if (this.validImageTypes.includes(fileType)) {
            return true;
          } else {
            this.promptErrorAlert("Hanya menerima file gambar JPG/PNG");
            return false;
          }
        } catch (error) {
          console.log(error);
          return false;
        }
      },
      validateSize() {
        // if (this.imageFile.size < 2048000) {
        //   return true;
        // } else {
        //   this.promptErrorAlert(`Ukuran ${this.fieldName} maksimal 2MB`);
        //   return false;
        // }
        return true;
      },
      async validateDimension() {
        let w = 0;
        let h = 0;
        let img = null;

        await this.loadImage().then(res => {
          img = res;
        });

        return new Promise((resolve, reject) => {
          try {
            w = img.width;
            console.log("image width", w);
            h = img.height;
            console.log("image height", h);
          } catch (err) {
            this.promptErrorAlert(
              "Fail to get image dimension. Please upload a proper image."
            );
            resolve(false);
          }

          if (w < 256) {
            this.promptErrorAlert(`Lebar ${this.fieldName} minimal 256px`);
            resolve(false);
          } else if (h < 256) {
            this.promptErrorAlert(`Tinggi ${this.fieldName} minimal 256px`);
            resolve(false);
          } else {
            resolve(true);
          }
        });
      },
      loadImage() {
        return new Promise((resolve, reject) => {
          const img = new Image();
          img.addEventListener("load", () => resolve(img));
          img.addEventListener("error", err => reject(err));
          img.src = URL.createObjectURL(this.imageFile);
        });
      }
    }
  };
</script>

<style lang="scss" scoped>
  $height: 150px;

  .verif-photo-input {
    $__self: &;

    &__label {
      font-size: 14px;
      color: #707070;
      font-weight: 600;
      margin-bottom: 5px;
    }

    &__box {
      $__box: &;
      height: $height;
      border: 1px dashed #b2b2b2;
      background-color: #fefefe;
      cursor: pointer;
      display: flex;
      justify-content: center;
      align-items: center;

      &__icon {
        width: 100px;
        opacity: 0.5;
        transition: transform 0.2s ease-in-out, opacity 0.2s ease-in-out;
      }

      &:hover {
        #{ $__box }__icon {
          opacity: 1;
          transform: scale(1.05);
        }
      }
    }

    &--is-error {
      #{ $__self }__label {
        color: #ee2211;
      }

      #{ $__self }__box {
        border: 1px dashed #ee2211;
        background-color: lighten(#ee2211, 47.5%);
      }
    }

    &__placeholder {
      $__placeholder: &;
      position: relative;
      height: $height;
      max-width: 100%;
      padding: 10px;
      border: 1px solid #b2b2b2;
      background-color: #fefefe;
      cursor: pointer;

      img {
        width: 100%;
        height: 100%;
        object-fit: scale-down;
      }

      &__overlay {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        // display: none;
        max-height: 0;
        opacity: 0;
        overflow-y: hidden;
        background-color: rgba(0, 0, 0, 0.5);
        transition: max-height 0.2s ease-in-out, opacity 0.2s ease-in-out;
        color: #ffffff;
        font-size: 16px;
        text-align: center;
        display: flex;
        justify-content: center;
        align-items: center;
      }

      &:hover {
        #{ $__placeholder }__overlay {
          max-height: $height;
          opacity: 1;
        }
      }
    }

    input {
      display: none;
    }
  }

  .message-enter-active {
    animation: message ease-out 0.2s;
    overflow-y: hidden;
  }

  .message-leave-active {
    animation: message ease-in 0.2s reverse;
    overflow-y: hidden;
  }

  @keyframes message {
    0% {
      max-height: 0;
    }

    100% {
      max-height: 11px;
    }
  }
</style>