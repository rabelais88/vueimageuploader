<template>
  <div>
    <div v-if="!imagebuff">
      <!-- TODO: change button type & class-->
      <button @click="addImage">image upload(Single)</button>
      <input type="file"
        accept="image/*"
        ref="fileloader"
        @change="onFileChange"
        hidden/>
    </div>
    <div v-else class="compCont">
      <div class="elImgCont">
        <div @click="removeImage" class="elImgRemove">
          <svg viewPort="0 0 12 12" version="1.1"
            xmlns="http://www.w3.org/2000/svg"
            width="12" height="12">
            <line x1="1" y1="11"
                  x2="11" y2="1"
                  stroke="white"
                  stroke-width="2"/>
            <line x1="1" y1="1"
                  x2="11" y2="11"
                  stroke="white"
                  stroke-width="2"/>
          </svg>
        </div>
        <img :src="imagebuff.src" />
      </div>
    </div>
  </div>
</template>
<script>

const defaultData = {
  imagebuff: '',
};

const props = {
  allowed: {
    type: String,
    default: 'jpeg,jpg,png,bmp',
  },
  maxSize: {
    type: Number,
    default: 1024 * 1024 * 2, // maximum of 2mb
  },
  maxWidth: {
    type: Number,
    default: 2000,
  },
  maxHeight: {
    type: Number,
    default: 2000,
  },
};

export default {
  data() {
    return defaultData;
  },
  props,
  computed: {
    allowedTypes() {
      return this.allowed.split(',');
    },
  },
  methods: {
    addImage() {
      const loader = this.$refs.fileloader;
      loader.click();
    },
    onFileChange(e) {
      const files = e.target.files || e.dataTransfer.files;
      if (files.length > 0) this.createImage(files[0]);
    },
    createImage(file) {
      const fileType = file.type.split('/')[1];
      if (this.allowedTypes.includes(fileType)) {
        const reader = new FileReader();
        reader.onload = this.onImageLoad;
        reader.readAsDataURL(file);
      } else {
        // TODO: use error code
        this.errEmit('err', 'file type error');
      }
    },
    onImageLoad(e) {
      this.imagebuff = new Image();
      this.imagebuff.src = e.target.result;
      const isValidated = this.validate(e.target.result.length);
      if (!isValidated) {
        this.imagebuff = '';
      }
    },
    validate(fileSize) {
      if (fileSize > this.maxSize) {
        // TODO: use error code
        this.errEmit('file size is too big');
        return false;
      } else if (this.imagebuff.width > this.maxWidth || this.imagebuff.height > this.maxHeight) {
        this.errEmit('image is too large');
        return false;
      }
      return true;
    },
    removeImage() {
      this.imagebuff = '';
    },
    errEmit(errmsg) {
      this.$emit('err', errmsg);
    },
  },
  watch: {
    imagebuff(imagebuff) {
      this.$emit('chosen', imagebuff);
    },
  },
};
</script>
<style lang="scss" scoped>
  // TODO: remove this temporary classes
  img{
    width:100%;
    height:100%;
    object-fit:cover;
  }
  .elImgCont{
    width: 100px;
    height: 100px;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    position:relative;
  }
  .elImgRemove{
    right: 0;
    top: 0;
    cursor: pointer;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    position: absolute;
    color: white;
    width: 25px;
    height: 25px;
  }
  .compCont{
    display:flex;
  }
</style>
