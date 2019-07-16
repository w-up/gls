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
          <div class="video_list" v-for="(videoitem,index) in videoList" :key="index">
            <!-- <video controls>
              <source :src="videoitem.url" />
            </video>-->
            <video-player
              class="video-player vjs-custom-skin"
              ref="videoPlayer"
              :playsinline="true"
              :options="videoitem.url"
            ></video-player>
            <span>{{videoitem.title}}</span>
          </div>
        </div>
      </div>
      <div v-if="videoIndex==1" class="tab-swiper vux-center">
        <div class="video">
          <div class="video_list" v-for="(videoitem,index) in videoList" :key="index">
            <!-- <video-player
              class="video-player vjs-custom-skin"
              ref="videoPlayer"
              :playsinline="true"
              :options="playerOptions"
            ></video-player>-->
            <span>{{videoitem.title}}</span>
          </div>
        </div>
      </div>
      <div v-if="videoIndex==2" class="tab-swiper vux-center">
        <div class="video">
          <div class="video_list" v-for="(videoitem,index) in videoList" :key="index">
            <video controls>
              <source :src="videoitem.url" />
            </video>
            <span>{{videoitem.title}}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
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
      playerOptions: {
        playbackRates: [0.7, 1.0, 1.5, 2.0], //播放速度
        autoplay: false, //如果true,浏览器准备好时开始回放。
        muted: false, // 默认情况下将会消除任何音频。
        loop: false, // 导致视频一结束就重新开始。
        preload: "auto", // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
        language: "zh-CN",
        aspectRatio: "16:9", // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
        fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
        sources: [
          {
            type: "",
            src:
              "http://hls01open.ys7.com/openlive/471db23430e2458cbe183e0863ab1de7.m3u8" //视频url地址
          }
        ],
        poster: "../../assets/img/tab1_on.png", //你的封面地址
        // width: document.documentElement.clientWidth,
        notSupportedMessage: "此视频暂无法播放，请稍后再试", //允许覆盖Video.js无法播放媒体源时显示的默认信息。
        controlBar: {
          timeDivider: true,
          durationDisplay: true,
          remainingTimeDisplay: false,
          fullscreenToggle: true //全屏按钮
        }
      }
    };
  },
  mounted: function() {
    let that = this;
    that.getVideoList();
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
    playerOptions(item, index) {
      const options = {
        playbackRates: [0.7, 1.0, 1.5, 2.0], // 播放速度
        autoplay: false, // 如果true,浏览器准备好时开始回放。
        muted: false, // 默认情况下将会消除任何音频。
        loop: false, // 导致视频一结束就重新开始。
        preload: "auto", // 建议浏览器在加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
        language: "zh-CN",
        aspectRatio: "16:9", // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
        fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
        poster: item.videoImg, //你的封面地址
        notSupportedMessage: "此视频暂无法播放，请稍后再试", // 允许覆盖Video.js无法播放媒体源时显示的默认信息。
        timeDivider: true,
        durationDisplay: true,
        remainingTimeDisplay: false,
        fullscreenToggle: false // 全屏按钮
      };
      return Object.assign(options, {
        sources: [
          {
            type: "video/mp4",
            src: item.video.src
          }
        ]
      });
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
  width: 94%;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.video .video_list {
  width: 47%;
  /* height: 3rem; */
  margin: 0.1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.video .video_list video {
  width: 100%;
  /* height: 2rem; */
  margin-bottom: 0.1rem;
  border: 1px solid #e5e5e5;
}
</style>
