<template>
  <div id="app" class="container">

    <h1>{{ msg }}</h1>

    <section id="step-1">
      <div class="page-header">
        <h3>Step 1: 上傳圖片</h3>
      </div>
      <div class="row">
        <div class="col-md-9">
          <div class="image-container">
            <img id="avatar-image" src="./images/keroro.jpg" alt="上傳圖片">
            <input type="file" id="upload-image" class="hidden" accept="image" @change="changeImage">
            <label for="upload-image" class="btn">上傳圖片</label>
          </div>
        </div>
        <div class="col-md-3">
          <div class="image-preview large"></div>
          <div class="image-preview medium"></div>
          <div class="image-preview small"></div>
          <button type="button" class="btn" @click="cropImage(0)" v-scroll-to="'#step-2'">選擇相框</button>
        </div>
      </div>
    </section>

    <section id="step-2">
      <div class="page-header">
        <h3>Step 2: 套用相框</h3>
      </div>
      <div class="row">
        <div class="col-md-12">
          <div class="frame-list">
            <div class="frame-item">
              <img src="./images/frame_01.png" alt="" class="frame" widtzh="200">
              <ul>
                <li>世大運 - 自己的同學自己挺</li>
                <li><a href="https://www.facebook.com/dcard.tw/" target="_blank">Dcard</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(0)" v-scroll-to="'#step-3'">套用</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_02.png" alt="" class="frame" widtzh="200">
              <ul>
                <li>世大運 - 自己的同學自己挺</li>
                <li><a href="https://www.facebook.com/dcard.tw/" target="_blank">Dcard</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(1)" v-scroll-to="'#step-3'">套用</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_03.png" alt="" class="frame" widtzh="200">
              <ul>
                <li>Cherng 驚嚇</li>
                <li><a href="https://www.facebook.com/cherng.y/" target="_blank">Cherng</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(2)" v-scroll-to="'#step-3'">套用</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_04.png" alt="" class="frame" widtzh="200">
              <ul>
                <li>好人信的大頭框</li>
                <li><a href="https://www.facebook.com/goodman5566/" target="_blank">好人信的生活日常</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(3)" v-scroll-to="'#step-3'">套用</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_05.png" alt="" class="frame" widtzh="200">
              <ul>
                <li>UGLYZOO</li>
                <li><a href="https://www.facebook.com/Uglilybook/" target="_blank">醜臉書</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(4)" v-scroll-to="'#step-3'">套用</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_06.png" alt="" class="frame" widtzh="200">
              <ul>
                <li>我是奧利佛</li>
                <li><a href="https://www.facebook.com/Uglilybook/" target="_blank">醜臉書</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(5)" v-scroll-to="'#step-3'">套用</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_07.png" alt="" class="frame" widtzh="200">
              <ul>
                <li>你，渴望力量嗎？</li>
                <li><a href="#" target="_blank">Tetsuko Wang</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(6)" v-scroll-to="'#step-3'">套用</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_08.png" alt="" class="frame" widtzh="200">
              <ul>
                <li>其實我不是胖 我只是懶得瘦</li>
                <li><a href="https://www.facebook.com/FunkyLook.Shop/" target="_blank">FunkyLook</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(7)" v-scroll-to="'#step-3'">套用</button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section id="step-3">
      <div class="page-header">
        <h3>Step 3: 分享照片</h3>
      </div>
      <div class="row">
        <div class="col-md-9">
          <canvas id="profile-image" width="400" height="400"></canvas>
        </div>
        <div class="col-md-3">
          <a v-if="showDownloadButton" class="btn download" :href="downloadImage" download="cropped.jpg">下載</a>
        </div>
      </div>
    </section>

  </div>
</template>

<script>
import Vue from 'vue'

import CropperStyle from './style/cropper.scss'
import AppStyle from './style/app.scss'

import CropperJS from 'cropperjs'
import VueScrollTo from 'vue-scrollto'

// set the defaults.
Vue.use(VueScrollTo, {
  container: 'body',
  duration: 500,
  easing: 'ease',
  offset: -30,
  cancelable: true,
  onDone: false,
  onCancel: false,
  x: false,
  y: true
})

export default {
  name: 'app',
  data() {
    return {
      msg: '製作大頭貼照',
      cropper: '',
      croppable: false,
      avatarPreview: '',
      avatarImage: '',
      localUploadUrl: '',
      frameIndex: 0,
      frameSrcAry: [
        './dist/images/frame_01.png', './dist/images/frame_02.png', './dist/images/frame_03.png',
        './dist/images/frame_04.png', './dist/images/frame_05.png', './dist/images/frame_06.png',
        './dist/images/frame_07.png', './dist/images/frame_08.png'
      ],
      frameImageAry: [],
      uploadImage: new Image(),
      downloadImage: '',
      showDownloadButton: false,
      profileImage:''
    }
  },
  mounted() {
    //CropperJS initialization
    this.avatarImage = document.getElementById('avatar-image');
    this.avatarPreview = document.getElementsByClassName('image-preview');
    this.profileImage = document.getElementById('profile-image');
    this.cropper = new CropperJS(this.avatarImage, {
      aspectRatio: 1,
      viewMode: 1,
      background: false,
      zoomable: false,
      background: true,
      ready: () => {
        this.cloneAvatarImage();
        this.croppable = true;
      },
      crop: (e) => {
        let data = e.detail,
          cropper = this.cropper,
          imageData = cropper.getImageData(),
          previewAspectRatio = data.width / data.height;

        this.each(this.avatarPreview, (elem) => {
          let previewImage = elem.getElementsByTagName('img').item(0),
            previewWidth = elem.offsetWidth,
            previewHeight = previewWidth / previewAspectRatio,
            imageScaledRatio = data.width / previewWidth;

          elem.style.height = previewHeight + 'px';
          previewImage.style.width = imageData.naturalWidth / imageScaledRatio + 'px';
          previewImage.style.height = imageData.naturalHeight / imageScaledRatio + 'px';
          previewImage.style.marginLeft = -data.x / imageScaledRatio + 'px';
          previewImage.style.marginTop = -data.y / imageScaledRatio + 'px';
        });
      }
    });

    this.loadImages(this.frameSrcAry, (frameImageAry) => {
      this.frameImageAry = frameImageAry;
    });

  },
  watch: {
  },
  methods: {
    each: function(arr, callback) {
      for (let i = 0, len = arr.length; i < len; i++) {
        callback.call(arr, arr[i], i, arr);
      }
      return arr;
    },


    cloneAvatarImage() {
      let avatarImageClone = this.avatarImage.cloneNode();
      avatarImageClone.className = ''
      avatarImageClone.style.cssText = (
        'display: block;' +
        'width: 100%;' +
        'min-width: 0;' +
        'min-height: 0;' +
        'max-width: none;' +
        'max-height: none;'
      );
      this.each(this.avatarPreview, (elem) => {
        while (elem.firstChild) {// remove old image.
          elem.removeChild(elem.firstChild);
        }
        elem.appendChild(avatarImageClone.cloneNode());
      });
    },


    changeImage(e) {
      let files = e.target.files || e.dataTransfer.files;
      if (!files.length) return;

      let file = files[0];

      if (!/^image\/\w+/.test(file.type)) {
        window.alert('Please choose an image file.');
        return;
      }

      if (file.size > 1024000) {
        window.alert('The image file is too large.');
        return;
      }

      this.localUploadUrl = this.getObjectURL(file);

      // Update url.
      if (this.cropper) {
        this.cropper.replace(this.localUploadUrl);
        this.cloneAvatarImage();
      }
    },


    cropImage(frameIndex) {
      let croppedCanvas, roundedCanvas;

      if (!this.croppable) return;
      // Crop
      croppedCanvas = this.cropper.getCroppedCanvas();
      // Round
      roundedCanvas = this.getRoundedCanvas(croppedCanvas);

      this.uploadImage.src = roundedCanvas.toDataURL();
      this.frameIndex = frameIndex;

      Vue.nextTick(() => {
        window.setTimeout(() => {
          this.mixImage();
        }, 0);
      });
    },


    getObjectURL(file) {
      let url = null;
      if (window.createObjectURL != undefined) { // basic
        url = window.createObjectURL(file);
      } else if (window.URL != undefined) { // mozilla(firefox)
        url = window.URL.createObjectURL(file);
      } else if (window.webkitURL != undefined) { // webkit or chrome
        url = window.webkitURL.createObjectURL(file);
      }
      return url;
    },


    getRoundedCanvas(sourceCanvas) {
      let canvas = document.createElement('canvas');
      let context = canvas.getContext('2d');

      const width = sourceCanvas.width;
      const height = sourceCanvas.height;

      canvas.width = width;
      canvas.height = height;

      context.drawImage(sourceCanvas, 0, 0, width, height);

      return canvas;
    },


    loadImages(sources, callback) {
      let images = [];
      let loadedImages = 0;
      let numImages = 0;
      for (var i = 0, len = sources.length; i < len; i++) {
        images[i] = new Image();
        images[i].onload = () => {
          if (++loadedImages >= len) {
            callback(images);
          }
        };
        images[i].src = sources[i];
      }
    },


    mixImage() {
      let context = this.profileImage.getContext("2d");

      context.clearRect(0, 0, this.profileImage.width, this.profileImage.height);

      // Set background fill.
      context.beginPath();
      context.rect(0, 0, this.profileImage.width, this.profileImage.height);
      context.fillStyle = "#FFF";
      context.fill();

      context.drawImage(this.uploadImage, 0, 0, this.profileImage.width, this.profileImage.height);
      context.drawImage(this.frameImageAry[this.frameIndex], 0, 0, this.profileImage.width, this.profileImage.height);

      this.downloadImage = this.profileImage.toDataURL('image/jpeg');
      this.showDownloadButton = true;
    },


  }
}

</script>
