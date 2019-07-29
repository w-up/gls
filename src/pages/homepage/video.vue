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
          <div id="id_test_video" style="width:100%; height:auto;"></div>
          <!-- <div class="video_list" v-for="(item, index) in listOptions1" :key="index">
            
            <span>{{item.videoTitle}}</span>
          </div>-->
        </div>
      </div>
      <div v-show="videoIndex==1" class="tab-swiper vux-center">
        <div class="video">
          <div class="video_list" v-for="(item, index) in listOptions2" :key="index">
            <video-player class="vjs-custom-skin" :options="item" @ready="playerReadied"></video-player>
            <span>{{item.videoTitle}}</span>
          </div>
        </div>
      </div>
      <div v-show="videoIndex==2" class="tab-swiper vux-center">
        <div class="video">
          <div class="video_list" v-for="(item, index) in listOptions3" :key="index">
            <video-player class="vjs-custom-skin" :options="item" @ready="playerReadied"></video-player>
            <span>{{item.videoTitle}}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
require("../../assets/js/TcPlayer");
import { Indicator, Toast } from "mint-ui";
import { Tab, TabItem } from "vux";
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
  mounted: function() {
    // this.getVideoList(1);
    // this.getVideoList(2);
    // this.getVideoList(3);
    (function() {
      function getParams(name) {
        var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
        var r = window.location.search.substr(1).match(reg);
        if (r != null) {
          return decodeURIComponent(r[2]);
        }
        return null;
      }

      var rtmp = getParams("rtmp"),
        flv = getParams("flv"),
        m3u8 = getParams("m3u8"),
        mp4 = getParams("mp4"),
        live = getParams("live") == "true" ? true : false,
        coverpic = getParams("coverpic"),
        width = getParams("width"),
        height = getParams("height"),
        autoplay = getParams("autoplay") == "true" ? true : false;
      /**
       * 视频类型播放优先级
       * mobile ：m3u8>mp4
       * PC ：RTMP>flv>m3u8>mp4
       */

      /**
       * 属性说明：
       *
       * coverpic  {Object|String} src:图片地址，style：default 居中1:1显示 stretch 拉伸铺满，图片可能会变形 cover 等比横向铺满，图片某些部分可能无法显示在区域内
       *  封面在 ios10 暂时无法生效。
       */
      var options = {
        rtmp: rtmp,
        flv: flv,
        m3u8: m3u8,
        mp4:
          mp4 ||
          "//1256993030.vod2.myqcloud.com/d520582dvodtransgzp1256993030/7732bd367447398157015849771/v.f30.mp4",
        coverpic: coverpic || {
          style: "cover",
          src:
            "//vodplayerinfo-10005041.file.myqcloud.com/3035579109/vod_paster_pause/paster_pause1469013308.jpg"
        },
        autoplay: autoplay ? true : false,
        live: live,
        width: width || "480",
        height: height || "320"
      };

      var player = new TcPlayer("video-container", options);
      window.qcplayer = player;
    })();
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
    playerReadied(player) {
      var hls = player.tech({ IWillNotUseThisInPlugins: true }).hls;
      player.tech_.hls.xhr.beforeRequest = function(options) {
        // console.log(options)
        return options;
      };
    },
    //获取视频列表
    getVideoList(index) {
      let that = this;
      // typev = 1 =>田园,typev = 2 =>果园,typev = 3 =>牧场
      // let typev = that.videoIndex == 0 ? 1 : that.videoIndex == 1 ? 2 : 3;
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
            if (index == 1) {
              for (let i = 0; i < res.data.data.length; i++) {
                that.listOptions1.push({
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

.video .video_list {
  width: 3.45rem;
  /* height: 2.346rem; */
  margin-bottom: 0.2rem;
  text-align: center;
}
.video_list span {
  font-size: 0.28rem;
  color: #333;
}
</style>
