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
      <div v-show="videoIndex==0" class="tab-swiper vux-center">
        <div class="video">
          <div class="video_list" v-for="(item, index) in listOptions1" :key="index">
            <video-player ref="videoPlayer" class="vjs-custom-skin" :options="item"  @ready="playerReadied"></video-player>
               <a class="play_video" ref="playVideo" @click="onPlay(index)"></a>
            <span>{{item.videoTitle}}</span>
          </div>
        </div>
      </div>
      <div v-show="videoIndex==1" class="tab-swiper vux-center">
        <div class="video">
          <div class="video_list" v-for="(item, index) in listOptions2" :key="index">
            <video-player class="vjs-custom-skin" :options="item"  @ready="playerReadied"></video-player>
             <a class="play_video" ref="playVideo" @click="onPlay(index)"></a>
            <span>{{item.videoTitle}}</span>
          </div>
        </div>
      </div>
      <div v-show="videoIndex==2" class="tab-swiper vux-center">
        <div class="video">
          <div class="video_list" v-for="(item, index) in listOptions3" :key="index">
            <video-player class="vjs-custom-skin" :options="item" @ready="playerReadied"></video-player>
             <a class="play_video" ref="playVideo" @click="onPlay(index)"></a>
            <span>{{item.videoTitle}}</span>
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
import FastClick from 'fastclick'
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
      listOptions1: [],
      listOptions2: [],
      listOptions3: [],
      playerOptions: {
        // videojs and plugin options
        // height: "360",
        autoplay: false, //如果true,浏览器准备好时开始回放。
        muted: false, // 默认情况下将会消除任何音频。
        preload: "auto", // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
        language: "zh-CN",
        width: document.documentElement.clientWidth,
        aspectRatio: "16:9", // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
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
   computed: {
      player() {
        return this.$refs.videoPlayer.player
      },
      },
  mounted: function() {
    this.getVideoList(1);
    this.getVideoList(2);
    this.getVideoList(3);
    FastClick.attach(document.body)
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
        // that.getVideoList();
      }
    },
    onPlay(index) {
     return this.$refs.videoPlayer[index].player.play();
    },
    // onPlayerPause(player){
    //   alert("pause");
    // },
    playerReadied(player) {
      // var hls = player.tech({ IWillNotUseThisInPlugins: true }).hls;
      // player.tech_.hls.xhr.beforeRequest = function(options) {
      //   // console.log(options)
      //   return options;
      // };
    },
    fullScreen() {
        const player = this.$refs.videoPlayer.player
        player.requestFullscreen()//调用全屏api方法
        player.isFullscreen(true)
        player.play()
      },
    //获取视频列表
    getVideoList(index) {
      let that = this;
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "Subscribe/videoList",
          method: "post",
          data: {
            type: index
          }
        })
        .then(function(res) {

          if (res.data.code == 0) {
            //成功回调
            that.videoList = res.data.data;
            // console.log(that.videoList)
            if (index == 1) {
              for (let i = 0; i < res.data.data.length; i++) {
                that.listOptions1.push({

                  videoTitle: res.data.data[i].title, // 视频名字
                  autoplay: false, //如果true,浏览器准备好时开始回放。
                  muted: false, // 默认情况下将会消除任何音频。
                  preload: "auto", // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
                  language: "zh-CN",
                  aspectRatio: "16:9", // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
                  fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
                  sources: [
                    {
                      withCredentials: false,
                      type: "application/x-mpegURL",
                      src: res.data.data[i].url
                    }
                  ],
                  controlBar: {
                    timeDivider: false,
                    durationDisplay: false,
                    fullscreenToggle: true //全屏按钮
                  },
                  flash: { hls: { withCredentials: false } },
                  html5: { hls: { withCredentials: false } },
                  poster: res.data.data[i].link
                });
              }
            } else if (index == 2) {
              for (let i = 0; i < res.data.data.length; i++) {
                that.listOptions2.push({
                  // videojs and plugin options
                  // height: "360",
                  videoTitle: res.data.data[i].title, // 视频名字
                  autoplay: false, //如果true,浏览器准备好时开始回放。
                  muted: false, // 默认情况下将会消除任何音频。
                  preload: "auto", // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
                  language: "zh-CN",
                  aspectRatio: "16:9", // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
                  fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
                  sources: [
                    {
                      withCredentials: false,
                      type: "application/x-mpegURL",
                      src: res.data.data[i].url
                    }
                  ],
                  controlBar: {
                    timeDivider: false,
                    durationDisplay: false,
                    fullscreenToggle: true //全屏按钮
                  },
                  flash: { hls: { withCredentials: false } },
                  html5: { hls: { withCredentials: false } },
                  poster: res.data.data[i].link
                });
              }
            } else if (index == 3) {
              for (let i = 0; i < res.data.data.length; i++) {
                that.listOptions3.push({
                  // videojs and plugin options
                  // height: "360",
                  videoTitle: res.data.data[i].title, // 视频名字
                  autoplay: false, //如果true,浏览器准备好时开始回放。
                  muted: false, // 默认情况下将会消除任何音频。
                  preload: "auto", // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
                  language: "zh-CN",
                  aspectRatio: "16:9", // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
                  fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
                  sources: [
                    {
                      withCredentials: false,
                      type: "application/x-mpegURL",
                      src: res.data.data[i].url
                    }
                  ],
                  controlBar: {
                    timeDivider: false,
                    durationDisplay: false,
                    fullscreenToggle: true //全屏按钮
                  },
                  flash: { hls: { withCredentials: false } },
                  html5: { hls: { withCredentials: false } },
                  poster: res.data.data[i].link
                });
              }
            }
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
  padding: 0.2rem;
}

.video {
  width: 100%;
  position: relative;
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-pack: justify;
  -ms-flex-pack: justify;
  justify-content: space-between;
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
}
/* /deep/.video-js.vjs-fullscreen{
  position: absolute !important;
  left: 0;
  top: 0;

} */
.video .video_list {
  width: 3.45rem;
  /* height: 2.346rem; */
  margin-bottom: 0.2rem;
  text-align: center;
  position: relative;
}
.video .video_list a.play_video{
  position: absolute;
  top: 0;
  left: 0;
  width: 3.45rem;
  height: 1.26rem;
  /* background: #0086B3; */
  opacity: 0;
  display: block;
  cursor:pointer

}
.video_list span {
  font-size: .28rem;
  color: #333;
}
</style>
