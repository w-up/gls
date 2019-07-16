<template>
	<div id="reserecord">
		<mt-header fixed title="预订记录">
			<a slot="left">
				<mt-button icon="back" @click="back">返回</mt-button>
			</a>
		</mt-header>
		<div class="con-wrapper">
			<div class="scroll_div">
			  <van-pull-refresh
			    v-model="isLoading"
			    pulling-text="下拉刷新"
			    loosing-text="释放更新"
			    loading-text="正在加载..."
			    @refresh="onRefresh"
			  >
			    <div
			      class="div"
			      v-infinite-scroll="loadMore"
			      infinite-scroll-disabled="loading"
			      infinite-scroll-distance="10"
			      infinite-scroll-immediate-check="false"
			    >
			<div class="record_content">
				<div class="yipin">
					<div class="record_list" v-for="(recorditem,index) in recordList" :key="index">
						<div class="record_img">
							<img :src="recorditem.img" />
						</div>
						<div class="record_con">
							<div class="record_title">
								<span></span>
								<span>{{recorditem.title}}</span>
							</div>
							<div class="record_price">
								<span>价格</span>
								<span>{{recorditem.spend}}</span>
								<span>数量 {{recorditem.number}}</span>
							</div>
							<div class="record_time">
								<span>出售时间</span>
								<span>{{recorditem.sell_time}}</span>
							</div>
							<div class="record_time">
								<span>预定时间</span>
								<span>{{recorditem.time}}</span>
							</div>
							
						</div>
						
					</div>
					
				</div>
			</div>
			  </div>
			    <load-more v-if="lif" :show-loading="load" tip="正在加载..."></load-more>
			    <load-more v-if="nif" :show-loading="none" tip="没有更多数据了"></load-more>
			  </van-pull-refresh>
			</div>
		</div>
	</div>
</template>
<script>
	import {
		Indicator,
		Toast
	} from "mint-ui";
	import { LoadMore } from "vux";
	export default {
		components: {
			Indicator,
			Toast,
			LoadMore
		},
		data() {
			return {
				pageindex:1,
				recordList:[],//预定记录
				load: true, //加载图标显示
				none: false, //加载图标隐藏
				lif: true, //正在加载中 显示
				nif: false, //没有更多数据了 隐藏
				loading: false, //下拉刷新
				isLoading: false //上拉加载更多
			};
		},
		mounted: function() {
			let that = this;
			that.getSubscribeLog()
		},
		methods: {
			back() {
				this.$router.go(-1); //返回上一层
			},	
			 //下拉刷新
			onRefresh() {
			  let that = this;
			  that.isLoading = true;
			  that.loading = false;
			  that.nif = false;
			  that.pageindex = 1;
			  that.recordList = [];
			  that.getSubscribeLog(1);
			},
			//上拉加载更多
			loadMore() {
			  let that = this;
			  that.lif = true;
			  that.pageindex++;
			  that.getSubscribeLog(0);
			},
			//获取预定记录
			getSubscribeLog(i) {
				let that = this;
				  if (i) {
				  that.lif = true;
				}
				Indicator.open({
					text: "加载中...",
				});
				that
					.$http({
						url: "Subscribe/subscribeLog",
						method: "post",
						data: {
							token: localStorage.getItem("token"),
							 p: that.pageindex
						}
					})
					.then(function(res) {
						 that.lif = false;
						that.isLoading = false;
						if (res.data.code == 0) {
							 //成功回调
							if (res.data.data.list != "") {
							  that.recordList = that.recordList.concat(res.data.data.list);
							} else {
							  that.nif = true;
							  that.loading = true;
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
			},
		}
	};
</script>

<style scoped="scoped">
	#reserecord {
		width: 100%;
		height: 100%;
		overflow: hidden;
	}
	.con-wrapper {
		position: fixed;
		width: 100%;
		height: calc(100% - 50px);
		overflow-x: hidden;
		overflow-y: scroll;
		top: 40px;
	}

	.mint-header {
		background: #ef6213;
	}
	.scroll_div {
	  width: 100%;
	  height: 100%;
	  width: 90%;
	  margin: 0 auto;
	  margin-top: 0.1rem;
	}
	.record_list {
		display: flex;
		margin: 0.2rem 0;
		height: 1.8rem;
		align-items: center;
	}

	.record_list .record_img {
		width: 26%;
		height: 90%;

	}

	.record_list .record_img img {
		width: 100%;
		height: 100%;
	}

	.record_list .record_con {
		margin-left: 0.2rem;
		flex: 1;
	}

	.record_list .record_con .record_title{
		font-size: 0.28rem;
		font-weight: bold;
	}


	.record_list .record_con .record_price,
	.record_list .record_con .record_time
	{
		padding: 0.08rem 0;
		font-size: 0.26rem;
	}

	.record_list .record_con .record_price span:nth-child(2) {
		color: #EF6213;
		padding-right: 0.2rem;
	}
</style>
