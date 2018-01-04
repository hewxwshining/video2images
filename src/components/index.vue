<template>
  <div class="container">
    <br />
    <div class="row">
      <div class="col-md-3">
        <select class="form-control" v-model="currentVideoName">
          <option v-for="value in videoList"> {{value}}
          </option>
        </select>
      </div>
    </div>
    <br />
    <div class="row">
      <video :src="video" controls="false" id="media" width="1280" height="720" crossOrigin="Anonymous">您的浏览器不支持 video 标签。</video>
    </div>
    <br />
    <div class="row">
      <div class="col-md-2">
        <button class="btn" @click="save(true)">保存Enable技能</button>
      </div>
      <div class="col-md-2">
        <button class="btn" @click="save(false)">保存Disable技能</button>
      </div>
      <div class="col-md-3">
        <div class="input-group">
          <input type="text" class="form-control" placeholder="请输入装备1的名称" aria-describedby="basic-addon2" v-model="equipName1">
          <span class="input-group-addon btn" id="basic-addon2" @click="saveZ(1)">保存装备1</span>
        </div>
      </div>
      <div class="col-md-3">
        <div class="input-group">
          <input type="text" class="form-control" placeholder="请输入装备2的名称" aria-describedby="basic-addon2" v-model="equipName2">
          <span class="input-group-addon btn" id="basic-addon2" @click="saveZ(2)">保存装备2</span>
        </div>
      </div>
      <div class="col-md-2">
        <button class="btn" @click="saveF()">保存对决画面</button>
      </div>
    </div>
    <br/>
    <br/>
    <br/>
  </div>
</template>
<script type="text/javascript">
import Canvas2Image from 'common/js/canvas2image'
import videoList from 'common/js/config'
export default {
  data() {
    return {
      basicInfo: {
        skill: [{ startX: 921, startY: 605, width: 70, height: 56 }, { startX: 1002, startY: 469, width: 64, height: 61 }, { startX: 1134, startY: 393, width: 67, height: 58 }],
        equip: [{ startX: 1207, startY: 94, width: 48, height: 43 }, { startX: 1207, startY: 171, width: 46, height: 44 }],
        fight: { startX: 0, startY: 0, width: 1280, height: 720 }
      },
      MIME_TYPE: 'image/bmp',
      equipName1: '',
      equipName2: '',
      currentVideoName: "ChengYaoJin",
      videoList: videoList,
      video: ''
    }
  },
  watch: {
    currentVideoName(newval, oldval) {
      this.video = 'VideoList/' + newval + "/" + newval + ".mp4"
    }
  },
  mounted() {
    this.video = 'VideoList/' + this.currentVideoName + '/' + this.currentVideoName + '.mp4';
  },
  methods: {
    save(temp) {
      var filepath = $("#media").attr("src").split('/');

      var filename = filepath[1];
      this.basicInfo.skill.forEach((obj, key) => {
        console.log(obj);
        var i = key + 1;
        if (temp) {
          var name = filename + i + 'Enable';
        } else {
          var name = filename + i + 'Disable'
        }
        this.downloadFile('media', name, obj.startX, obj.startY, obj.width, obj.height);
      });
    },
    saveZ(temp) {
      if (temp == 1) {
        var name = this.equipName1 + 1;
        this.downloadFile('media', name, this.basicInfo.equip[0].startX, this.basicInfo.equip[0].startY, this.basicInfo.equip[0].width, this.basicInfo.equip[0].height);
      } else {
        var name = this.equipName2 + 2;
        this.downloadFile('media', name, this.basicInfo.equip[1].startX, this.basicInfo.equip[1].startY, this.basicInfo.equip[1].width, this.basicInfo.equip[1].height);
      }
    },
    saveF() {
      var name = "HeroVS";
      this.downloadFile('media', name, this.basicInfo.fight.startX, this.basicInfo.fight.startY, this.basicInfo.fight.width, this.basicInfo.fight.height);
    },
    downloadFile(mediaid, fileName, startx, starty, width, height) {

      var imgUrlBlob = this.toImgUrlBlob(mediaid, startx, starty, width, height);
      var aLink = document.createElement('a');

      aLink.download = fileName + ".bmp";

      aLink.href = URL.createObjectURL(imgUrlBlob);
      aLink.dataset.downloadurl = [this.MIME_TYPE, aLink.download, aLink.href].join(':');
      document.body.appendChild(aLink);
      aLink.click();
      document.body.removeChild(aLink);
    },

    toImgUrlBlob(mediaid, startx, starty, width, height) {

      var media = document.getElementById(mediaid);
      var canvas = document.createElement('canvas');
      var retCtx = canvas.getContext('2d');
      canvas.width = width;
      canvas.height = height;
      retCtx.drawImage(media, startx, starty, width, height, 0, 0, width, height);

      var imgURL = canvas.toDataURL(this.MIME_TYPE);
      var blob = this.base64Img2Blob(imgURL); //new Blob([content]);
      return blob;
    },

    base64Img2Blob(code) {
      var parts = code.split(';base64,');
      var contentType = parts[0].split(':')[1];
      var raw = window.atob(parts[1]);
      var rawLength = raw.length;

      var uInt8Array = new Uint8Array(rawLength);

      for (var i = 0; i < rawLength; ++i) {
        uInt8Array[i] = raw.charCodeAt(i);
      }

      return new Blob([uInt8Array], { type: contentType });
    },

    exportCanvasAsPNG(id, fileName) {

      var canvasElement = document.getElementById(id);

      var MIME_TYPE = "image/png";

      var imgURL = canvasElement.toDataURL(MIME_TYPE);

      var dlLink = document.createElement('a');
      dlLink.download = fileName;
      dlLink.href = imgURL;
      dlLink.dataset.downloadurl = [MIME_TYPE, dlLink.download, dlLink.href].join(':');

      document.body.appendChild(dlLink);
      dlLink.click();
      document.body.removeChild(dlLink);
    }

  }
}

</script>
<style type="text/css" scoped>


</style>
