<template>
  <div class="upload-container">
    <el-col :span="24">
      <el-upload
        :data="extData"
        :multiple="false"
        :show-file-list="false"
        :on-success="handleImageSuccess"
        :before-upload="beforeUpload"
        class="image-uploader"
        drag
        :http-request="customerUpload"
        action="http://localhost:8002/common/upload/upload">

        <i class="el-icon-upload" />
        <div class="el-upload__text">
          将文件拖到此处，或<em>点击上传</em>
        </div>
      </el-upload>
      <div class="image-preview image-app-preview">
        <div v-show="imagePath" class="image-preview-wrapper">
          <img :src="imagePath">
          <div class="image-preview-action">
            <i class="el-icon-delete" @click="rmImage" />
          </div>
        </div>
      </div>
    </el-col>
  </div>
</template>

<script>
import {fileUpload} from '@/api/common/upload'

export default {
  name: 'CommonUpload',
  props: {
    imageUrl:{
      type: String,
      default: ''
    },
    extData:{
      type: Object,
      default: function () {
        return new Object()
      }
    }
  },
  data(){
    return {
      imagePath: this.imageUrl
    }
  },
  watch: {
    imageUrl (val) {
      this.imagePath = val
    }
  },
  methods: {
    rmImage() {

    },
    beforeUpload(file) {
      const isJPG = file.type === 'image/jpeg';
      const isGIF = file.type === 'image/gif';
      const isPNG = file.type === 'image/png';
      const isBMP = file.type === 'image/bmp';
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG && !isGIF && !isPNG && !isBMP) {
        this.$message.error('上传图片必须是JPG/GIF/PNG/BMP 格式!');
      }
      if (!isLt2M) {
        this.$message.error('上传图片大小不能超过 2MB!');
      }
      return (isJPG || isBMP || isGIF || isPNG) && isLt2M;
    },
    handleImageSuccess(res, file) {
      this.imagePath = URL.createObjectURL(file.raw);
      this.$emit('imageUploadEvent',res.result);
    },
    customerUpload(file) {
      const _self = this
      return new Promise((resolve, reject) => {
        fileUpload(file).then(response => {
          file.onSuccess(response);
        }).catch(err => { console.error(err) ;reject(true)})
      })
    }
  }
}
</script>
<style lang="scss" scoped>
  @import "@/styles/mixin.scss";
  .upload-container {
    width: 100%;
    position: relative;
    @include clearfix;
    .image-uploader {
      width: 35%;
      float: left;
    }
    .image-preview {
      height: 200px;
      max-width: 380px;
      min-width: 200px;
      position: relative;
      border: 1px dashed #d9d9d9;
      float: left;
      margin-left: 20px;
      .image-preview-wrapper {
        position: relative;
        width: 100%;
        height: 100%;
        img {
          width: 100%;
          height: 100%;
        }
      }
      .image-preview-action {
        position: absolute;
        width: 100%;
        height: 100%;
        left: 0;
        top: 0;
        cursor: default;
        text-align: center;
        color: #fff;
        opacity: 0;
        font-size: 20px;
        background-color: rgba(0, 0, 0, .5);
        transition: opacity .3s;
        cursor: pointer;
        text-align: center;
        line-height: 200px;
        .el-icon-delete {
          font-size: 24px;
        }
      }
      &:hover {
        .image-preview-action {
          opacity: 1;
        }
      }
    }
    .image-app-preview {
      height: 200px;
      position: relative;
      border: 1px dashed #d9d9d9;
      float: left;
      margin-left: 20px;
      .app-fake-conver {
        height: 44px;
        position: absolute;
        width: 100%; // background: rgba(0, 0, 0, .1);
        text-align: center;
        line-height: 64px;
        color: #fff;
      }
    }
  }
</style>
