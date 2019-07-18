<template>
  <div>
    <div class="item" style="width:100%">
      <div class="player">
        <video-player class="vjs-custom-skin" :options="playerOptions" @ready="playerReadied"></video-player>
      </div>
    </div>
  </div>
</template>

<script>
// custom skin css
// import '../src/custom-theme.css'
// videojs
import videojs from "video.js";
window.videojs = videojs;
// hls plugin for videojs6
require("videojs-contrib-hls/dist/videojs-contrib-hls.js");
// export
export default {
  data() {
    return {
      playerOptions: {
        // videojs and plugin options
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
        poster: "https://surmon-china.github.io/vue-quill-editor/static/images/surmon-3.jpg"
      }
    };
  },
  methods: {
    playerReadied(player) {
      var hls = player.tech({ IWillNotUseThisInPlugins: true }).hls;
      player.tech_.hls.xhr.beforeRequest = function(options) {
        // console.log(options)
        return options;
      };
    }
  }
};
</script>