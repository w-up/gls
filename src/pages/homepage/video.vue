<template>
  <div id="video">
    <mt-header fixed title="在线视频">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <tab :line-width="2" bar-active-color="#ef6213" active-color="#ef6213" :scroll-threshold="5">
        <tab-item selected @on-item-click="setvideoIndex(0)">田园</tab-item>
        <tab-item @on-item-click="setvideoIndex(1)">果园</tab-item>
        <tab-item @on-item-click="setvideoIndex(2)">牧场</tab-item>
      </tab>
      <div v-if="videoIndex==0" class="tab-swiper vux-center">
        <div class="video">
          <div class="video_list">
            <video-player class="video-player vjs-custom-skin" :options="playerOptions" @ready="playerReadied"></video-player>
          </div>
          <div class="video_list">
            <video-player class="vjs-custom-skin" :options="playerOptions" @ready="playerReadied"></video-player>
          </div>
          <div class="video_list">
            <video-player class="vjs-custom-skin" :options="playerOptions" @ready="playerReadied"></video-player>
          </div>
        </div>
      </div>
      <div v-if="videoIndex==1" class="tab-swiper vux-center">
        <div class="video">
          <div class="item" style="width:46%; display:inline-block; margin-top: 10px;">
            <video-player class="vjs-custom-skin" :options="playerOptions" @ready="playerReadied"></video-player>
          </div>
          <div class="item" style="width:46%; display:inline-block; margin-top: 10px;">
            <video-player class="vjs-custom-skin" :options="playerOptions" @ready="playerReadied"></video-player>
          </div>
          <div class="item" style="width:46%; display:inline-block; margin-top: 10px;">
            <video-player class="vjs-custom-skin" :options="playerOptions" @ready="playerReadied"></video-player>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Indicator, Toast } from "mint-ui";
import { Tab, TabItem } from "vux";
import "videojs-flash";
// videojs
import videojs from "video.js";
window.videojs = videojs;
// hls plugin for videojs6
require("videojs-contrib-hls/dist/videojs-contrib-hls.js");
export default {
  components: {
    Tab,
    TabItem,
    Indicator,
    Toast
  },
  data() {
    return {
      videoIndex: 0,
      // type: 1,
      videoList: "",
      playerOptions: {
        // videojs and plugin options
        height: "360",
        autoplay: false, //如果true,浏览器准备好时开始回放。
        muted: false, // 默认情况下将会消除任何音频。
        preload: "auto", // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
        language: "zh-CN",
        // aspectRatio: "16:9", // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
        fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
        sources: [
          {
            withCredentials: false,
            type: "application/x-mpegURL",
            src:
              "http://hls01open.ys7.com/openlive/471db23430e2458cbe183e0863ab1de7.m3u8"
          }
        ],
        controlBar: {
          timeDivider: false,
          durationDisplay: false,
          fullscreenToggle: true //全屏按钮
        },
        flash: { hls: { withCredentials: false } },
        html5: { hls: { withCredentials: false } },
        poster:
          "https://surmon-china.github.io/vue-quill-editor/static/images/surmon-3.jpg"
      }
    };
  },
  mounted: function() {
    let that = this;
    // that.getVideoList();
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    // 田园，果园，牧场
    setvideoIndex(index) {
      // console.log(index);
      let that = this;
      if (index != that.videoIndex) {
        that.videoIndex = index;

        that.getVideoList();
      }
    },
    playerReadied(player) {
      var hls = player.tech({ IWillNotUseThisInPlugins: true }).hls;
      player.tech_.hls.xhr.beforeRequest = function(options) {
        // console.log(options)
        return options;
      };
    },
    //获取视频列表
    getVideoList() {
      let that = this;
      // typev = 1 =>田园,typev = 2 =>果园,typev = 3 =>牧场
      let typev = that.videoIndex == 0 ? 1 : that.videoIndex == 1 ? 2 : 3;
      console.log(typev);
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "Subscribe/videoList",
          method: "post",
          data: {
            type: typev
          }
        })
        .then(function(res) {
          console.log(res);
          if (res.data.code == 0) {
            //成功回调
            that.videoList = res.data.data;
            console.log(that.videoList);
            that.playerOptions.sources = that.videoList;
          } else {
            //失败
            Toast(res.data.msg);
          }
          Indicator.close();
        })
        .catch(function(error) {
          Indicator.close();
          Toast({
            message: "网络连接失败",
            position: "bottom",
            duration: 5000
          });
        });
    }
  }
};
</script>

<style scoped="scoped">
#video {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - 0.8rem);
  overflow-x: hidden;
  overflow-y: scroll;
  top: 0.8rem;
}

.mint-header {
  background: #ef6213;
}

.tab-swiper {
  width: 100%;
}

.video {
  width: 100%;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
  position: relative;
}

.video .video_list {
  width: 47%;
  /* height: 3rem; */
  margin: 0.1rem;
}

/* .video .video_list video {
  width: 100%;
  margin-bottom: 0.1rem;
  border: 1px solid #e5e5e5;
} */
</style>
