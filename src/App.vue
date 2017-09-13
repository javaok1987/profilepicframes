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
              <img src="./images/frame_01.png" alt="" class="frame">
              <ul>
                <li>青春的主場，自己挺</li>
                <li>
                  <a href="https://www.facebook.com/dcard.tw/" target="_blank">Dcard</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(0)" v-scroll-to="'#step-3'">Apply</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_02.png" alt="" class="frame">
              <ul>
                <li>世大運 - 自己的同學自己挺</li>
                <li>
                  <a href="https://www.facebook.com/dcard.tw/" target="_blank">Dcard</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(1)" v-scroll-to="'#step-3'">Apply</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_03.png" alt="" class="frame">
              <ul>
                <li>Cherng 驚嚇</li>
                <li>
                  <a href="https://www.facebook.com/cherng.y/" target="_blank">Cherng</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(2)" v-scroll-to="'#step-3'">Apply</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_04.png" alt="" class="frame">
              <ul>
                <li>好人信的大頭框</li>
                <li>
                  <a href="https://www.facebook.com/goodman5566/" target="_blank">好人信的生活日常</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(3)" v-scroll-to="'#step-3'">Apply</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_05.png" alt="" class="frame">
              <ul>
                <li>UGLYZOO</li>
                <li>
                  <a href="https://www.facebook.com/Uglilybook/" target="_blank">醜臉書</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(4)" v-scroll-to="'#step-3'">Apply</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_07.png" alt="" class="frame">
              <ul>
                <li>你，渴望力量嗎？</li>
                <li>
                  <a href="#" target="_blank">Tetsuko Wang</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(5)" v-scroll-to="'#step-3'">Apply</button>
            </div>
            <div class="frame-item">
              <img src="./images/frame_08.png" alt="" class="frame">
              <ul>
                <li>其實我不是胖 我只是懶得瘦</li>
                <li>
                  <a href="https://www.facebook.com/FunkyLook.Shop/" target="_blank">FunkyLook</a> 建立</li>
              </ul>
              <button type="button" class="btn" @click="cropImage(6)" v-scroll-to="'#step-3'">Apply</button>
            </div>
            <div id="custom-frame" class="frame-item">
              <div class="frame">
                客製自己的相框
              </div>
              <button type="button" class="btn" @click="cropImage(-1)" v-scroll-to="'#step-3'">Apply</button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section id="step-3">
      <div class="page-header">
        <h3>Step 3: 下載大頭貼照</h3>
      </div>
      <div class="row">
        <div class="col-md-9">
          <div id="custom-wrap" @dragover.prevent @drop="dragEnd" @dragstart="dragStart">
            <canvas id="profile-image" width="400" height="400"></canvas>
            <div id="custom-container"></div>
          </div>
        </div>
        <div class="col-md-3">
          <a v-if="botton.defaultDownload" class="btn download" :href="image.default" download="cropped.jpg">Download</a>
          <div v-if="botton.customDownload">
            <label for="">
              <input type="text" placeholder="建立文字" v-model="inputTextValue" maxlength="10"> 可調整圖片位置
            </label>
            <a class="btn download" @click="downloadCustomImage">Download</a>
            <div id="custom-text-list"></div>
          </div>
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
      frames: {
        index: 0,
        pathAry: [
          './images/frame_01.png', './images/frame_02.png', './images/frame_03.png',
          './images/frame_04.png', './images/frame_05.png',
          './images/frame_07.png', './images/frame_08.png'
        ],
        imageAry: [],
      },
      uploadImage: new Image(),
      image: { // 製作好的大頭貼照.
        default: '',
        custom: '',
      },
      botton: {// 按鈕.
        defaultDownload: false,
        customDownload: false,
      },
      profileImage: '',
      custom: {
        container: '',
        inputImage: '',
        inputText: '',
        textList: '',
      },
      inputTextValue: '',
      mouse: {
        positionX: '',
        positionY: '',
      }
    }
  },
  mounted() {
    this.avatarImage = document.getElementById('avatar-image');
    this.avatarPreview = document.getElementsByClassName('image-preview');
    this.profileImage = document.getElementById('profile-image');
    this.customWrap = document.getElementById('custom-wrap');
    this.custom.container = document.getElementById('custom-container');

    //CropperJS initialization
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

    this.loadImages(this.frames.pathAry, (frameImageAry) => {
      this.frames.imageAry = frameImageAry;
    });

    document.addEventListener('dragover', function(e) {
      e.preventDefault();
    }, false);

    // Add text-border.
    if (CanvasRenderingContext2D != 'undefined') {
      CanvasRenderingContext2D.prototype.outlineText =
        function outlineText(txt, x, y) {
          this.fillText(txt, x - 1, y);
          this.fillText(txt, x, y - 1);
          this.fillText(txt, x + 1, y);
          this.fillText(txt, x, y + 1);
        }
    }

  },
  watch: {
    inputTextValue: function(newVal, oldVal) {
      this.customSelfCanvas(newVal)
    }
  },
  methods: {
    clearCustomDOMTree: function() {// remove old image.
      while (this.custom.textList && this.custom.textList.firstChild) {
        this.custom.textList.removeChild(this.custom.textList.firstChild);
      }
      while (this.custom.container && this.custom.container.firstChild) {
        this.custom.container.removeChild(this.custom.container.firstChild);
      }
    },


    dragStart: function(e) {
      this.mouse.positionX = e.clientX;
      this.mouse.positionY = e.clientY;
    },


    dragEnd: function(e) {
      this.custom.inputImage.style.top = `${parseInt(this.custom.inputImage.style.top || 0, 10) + (e.clientY - this.mouse.positionY)}px`;
      this.custom.inputImage.style.left = `${parseInt(this.custom.inputImage.style.left || 0, 10) + (e.clientX - this.mouse.positionX)}px`;
    },


    customSelfCanvas: function(inputTextValue) {

      this.custom.textList = document.getElementById('custom-text-list');
      if (!this.custom.textList) return;//WTF??

      let customInputText = document.createElement('div');
      customInputText.innerHTML = inputTextValue;
      customInputText.className = 'custom-input';
      this.custom.inputText = customInputText;

      this.clearCustomDOMTree();

      this.custom.textList.appendChild(customInputText);

      setTimeout(() => {
        let canvas = document.createElement('canvas');
        let context = canvas.getContext('2d');
        let customImg = new Image();

        canvas.width = this.custom.inputText.offsetWidth + 10;
        canvas.height = this.custom.inputText.offsetHeight;

        context.font = 'bold 36px sans-serif';
        context.fillStyle = '#000';
        context.outlineText(inputTextValue, 0, canvas.height * .8);

        context.fillStyle = '#fff';
        context.fillText(inputTextValue, 0, canvas.height * .8);

        this.custom.inputImage = customImg;
        customImg.draggable = 'true';
        customImg.className = 'custom-img'
        customImg.src = canvas.toDataURL('image/png');
        customImg.draggable = true;

        this.custom.container.appendChild(customImg);
      }, 0);
    },


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
      this.frames.index = frameIndex;

      Vue.nextTick(() => {
        window.setTimeout(() => {
          this.mixProfileImage();
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


    mixProfileImage() {
      let context = this.profileImage.getContext('2d');

      // Clear canvas.
      context.setTransform(1, 0, 0, 1, 0, 0);
      context.clearRect(0, 0, this.profileImage.width, this.profileImage.height);

      // Set background fill.
      context.beginPath();
      context.rect(0, 0, this.profileImage.width, this.profileImage.height);
      context.fillStyle = '#FFF';
      context.fill();

      context.drawImage(this.uploadImage, 0, 0, this.profileImage.width, this.profileImage.height);

      if (this.frames.index === -1) {

        this.botton.customDownload = true;
        this.botton.defaultDownload = false;
        this.inputTextValue = '';
      } else {
        this.clearCustomDOMTree();

        context.drawImage(this.frames.imageAry[this.frames.index], 0, 0, this.profileImage.width, this.profileImage.height);
        this.image.default = this.profileImage.toDataURL('image/jpeg');
        this.botton.defaultDownload = true;
        this.botton.customDownload = false;
      }
    },

    downloadCustomImage: function() {
      let context = this.profileImage.getContext('2d');
      const width = this.custom.inputText.offsetWidth;
      const height = this.custom.inputText.offsetHeight;
      const top = parseInt(this.custom.inputImage.style.top || 0, 10);
      const left = parseInt(this.custom.inputImage.style.left || 0, 10);
      let downloadLink;
      context.drawImage(document.getElementsByClassName('custom-img')[0], left, top, width, height);
      this.image.custom = this.profileImage.toDataURL('image/jpeg');

      this.clearCustomDOMTree();

      this.botton.customDownload = false;
      this.botton.defaultDownload = false;

      downloadLink = document.createElement('a');
      downloadLink.download = 'cropped.jpg';
      downloadLink.href = this.image.custom;
      downloadLink.click();
    },


  }
}

</script>
