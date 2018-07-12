<template>
  <div>
    {{isFocused}}
    <div class="Shadow"
      v-if="isFocused"
      :style="{zIndex}"
      @click="isFocused=false">
    </div>
    <form
      :style="{zIndex:zIndex + 1}"
      @click="isFocused=true">

      <ImageUploadMulti @err="erroralert" @chosen="ctx => images = ctx"></ImageUploadMulti>
      <ImageUploadSingle @err="erroralert" @chosen="ctx => image = ctx"></ImageUploadSingle>
    </form>
  </div>
</template>
<script>
import ImageUploadMulti from '@/components/ImageUploadMulti.vue';
import ImageUploadSingle from '@/components/ImageUploadSingle.vue';

const defaults = {
  testvar: '',
  isFocused: false,
  images: [],
  image: '',
};

function defaultData() {
  return defaults;
}

const props = {
  zIndex: {
    type: Number,
    default: 99,
  },
};

const components = {
  ImageUploadMulti,
  ImageUploadSingle,
};

export default {
  data: defaultData,
  props,
  components,
  methods: {
    onChange() {
      // TODO
    },
    erroralert(msg) {
      // eslint-disable-next-line
      alert(msg);
    },
    changed(ctx) {
      console.log(ctx);
    },
  },
};
</script>
<style lang="scss" scoped>
  .Shadow{
    position:fixed;
    background-color:rgba(0, 0, 0, .4);
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 99; // default is 99
    transition: opacity .3s ease;
  }
  form{
    background-color:white;
    position:relative;
    z-index:100; // 1 layer higher than Shadow
    width:50%;
    border:solid 1px gray;
  }

</style>
