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
					<div class="video_list" v-for="(videoitem,index) in videoList" :key="videoitem.id">
						<video controls>
							<source :src="videoitem.url">
						</video>
						<span>{{videoitem.title}}</span>
					</div>
				</div>
			</div>
			<div v-if="videoIndex==1" class="tab-swiper vux-center">
				<div class="video">
					<div class="video_list" v-for="(videoitem,index) in videoList" :key="videoitem.id">
						<video controls>
							<source :src="videoitem.url" type="video/mp4">
						</video>
						<span>{{videoitem.title}}</span>
					</div>
				</div>
			</div>
			<div v-if="videoIndex==2" class="tab-swiper vux-center">
				<div class="video">
					<div class="video_list" v-for="(videoitem,index) in videoList" :key="videoitem.id">
						<video controls>
							<source :src="videoitem.url" type="video/mp4">
						</video>
						<span>{{videoitem.title}}</span>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
	import {
		Indicator,
		Toast
	} from "mint-ui";
	import {
		Tab,
		TabItem,
	} from 'vux'
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
				videoList: ''

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
			//获取视频列表
			getVideoList() {
				let that = this;
				// typev = 1 =>田园,typev = 2 =>果园,typev = 3 =>牧场
				let typev = that.videoIndex == 0 ? 1 : (that.videoIndex == 1 ? 2 : 3);
				console.log(typev)
				Indicator.open({
					text: "加载中...",
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
						console.log(res)
						if (res.data.code == 0) {
							//成功回调
							that.videoList = res.data.data;
							console.log(that.videoList)
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
			},
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
		height: calc(100% - .8rem);
		overflow-x: hidden;
		overflow-y: scroll;
		top: .8rem;
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
		width: 100%;
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
