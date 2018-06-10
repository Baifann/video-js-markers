<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div>
      <video-player class="vjs-custom-skin"
                    ref="videoPlayer"
                    :options="playerOptions"
                    @timeupdate="onPlayerTimeupdate($event)"
                    @ready="playerReadied"
                    @pause="onPlayerPause($event)"
                    @play="onPlayerPlay($event)">
      </video-player>

      <button @click="onClickAddMakers">添加新的makers</button>

      <div v-show="isShowDot" class="ly-video-progress-dot" :style="popStyle">
        <div>是否在这里</div>
        <el-button type="danger" size="mini">否</el-button>
        <el-button type="primary" size="mini">是</el-button>
      </div>
    </div>
  </div>
</template>

<script>
import videojs from "video.js";
import { videoPlayer } from "vue-video-player";
import $ from "jquery";

window.videojs = videojs;

require("@/util/videojs-markers.js");

export default {
  name: "HelloWorld",

  components: {
    videoPlayer
  },

  data() {
    return {
      msg: "Welcome to Your Vue.js App",
      playerOptions: {
        height: "360",
        autoplay: true,
        muted: true,
        language: "en",
        playbackRates: [0.7, 1.0, 1.5, 2.0],
        sources: [
          {
            type: "video/mp4",
            // mp4
            src: "http://vjs.zencdn.net/v/oceans.mp4"
            // webm
            // src: "https://cdn.theguardian.tv/webM/2015/07/20/150716YesMen_synd_768k_vp8.webm"
          }
        ],
        poster:
          "https://surmon-china.github.io/vue-quill-editor/static/images/surmon-1.jpg"
      },
      player: {},
      popStyle: null,
      visible: true,
      isShowDot: false
    };
  },

  mounted() {
    this.initVideo();
  },

  methods: {
    initVideo() {
      var options = {};
    },
    playerReadied(player) {
      player.markers({
        markerStyle: {
          width: "8px",
          "background-color": "white"
        },
        markers: [
          { time: 9.5, text: "this" },
          { time: 16, text: "is" },
          { time: 23.6, text: "so" },
          { time: 28, text: "cool" }
        ]
      });

      this.player = player;

      console.log("playerReadied", player.markers);
      // 因为伊布的关系 并不会马上 返回这个markers
      console.log(player.markers.getMarkers());
    },
    onClickAddMakers() {
       console.log(this.player.markers.getMarkers());

      console.log("onClickAddMakers", this.player.markers);

      this.player.markers.add([
        { time: 40, text: "特别长的字符，这个是因为什么引起的" },
      ]);
      this.player.markers.updateTime(true);
    },
    onPlayerTimeupdate(player) {
      console.log(player.currentTime());

      const elements = $(".vjs-play-progress>.vjs-time-tooltip");
      console.log(elements);

      const element = elements[0];

      const left = this.getLeft(element);
      const top = this.getTop(element);
      
      this.popStyle = {left: `${left}px`, top : `${top - 25}px`};

      console.log(this.getLeft(element), this.getTop(element));
   },
   onPlayerPlay(player) {
     this.isShowDot = false;
   },
   onPlayerPause(player) {
     this.isShowDot = true;
   },
   getLeft(element) {
     return element.getBoundingClientRect().left+document.documentElement.scrollLeft;
   },
   getTop(element) {
     return element.getBoundingClientRect().top+document.documentElement.scrollTop;
   }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.ly-video-progress-dot {
  position: fixed;
  background-color: white;
  z-index: 4;
}
</style>
