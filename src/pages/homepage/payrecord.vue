<template>
	<div id="payrecord">
		<mt-header fixed title="付款记录">
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
					<div class="div" 
					v-infinite-scroll="loadMore" 
					infinite-scroll-disabled="loading" 
					infinite-scroll-distance="10"
					infinite-scroll-immediate-check="false"
					>
						<div class="pay_con">
							<div class="pay_list" v-for="(item,index) in payList" :key="index">
								<div class="pay_re">
									<span>{{item.name}}</span>
									<span>识别码:{{item.code}}</span>
									<span>{{item.money}}</span>
								</div>
								<div class="pay_time">
									<span>{{item.type}}</span>
									<span>{{item.time}}</span>
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
		Toast,
		Indicator,
	} from 'mint-ui';
	import {
		LoadMore,
	} from 'vux';
	export default {
		components: {
			Toast,
			LoadMore,
			Indicator,
		},
		data() {
			return {
				payList: [],
				pageindex: 1,
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
			that.getPayList()
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
				that.payList = [];
				that.getPayList(1);
			},
			//上拉加载更多
			loadMore() {
				let that = this;
				that.lif = true;
				that.pageindex++;
				that.getPayList(0);
			},
			//获取领养消费记录
			getPayList(i) {
				let that = this;
				if (i) {
					that.lif = true;
				}
				Indicator.open({
					text: "加载中...",
				});
				that.$http({
						url: "Subscribe/underShopLog",
						method: "post",
						data: {
							token: window.sessionStorage.getItem('token'),
							p: that.pageindex
						},
					})
					.then(function(res) {
						that.lif = false;
						that.isLoading = false;
						if (res.data.code == 0) {
							//成功回调
							if (res.data.data.list != "") {
								that.payList = that.payList.concat(res.data.data.list);
							} else {
								that.nif = true;
								that.loading = true;
							}
						} else {
							Toast('获取信息失败');
						}
						Indicator.close();
					})
					.catch(function(error) {
						Indicator.close();
					});
			},
		}
	};
</script>

<style scoped="scoped">
	#adoptRecord {
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
	.pay_con .pay_list {
		border-bottom: 1px solid #E9E9E9;
		padding: 0 0.2rem;
		font-size: 0.26rem;
		padding: 0.2rem 0;
	}
	.pay_con .pay_re {
		padding-bottom: 0.16rem;
		display: flex;
		justify-content: space-between;
	}
	.pay_con .pay_re span {
		color: #333;
	}
	.pay_con .pay_re span:nth-child(2) {
		padding-right: 2rem;
	}
	.pay_con .pay_time {
		display: flex;
		justify-content: space-between;
	}
	.pay_con .pay_time span:nth-child(2) {
		color: #777777;
	}
	.pay_con .pay_time span:nth-child(1) {
		color: #EF6213;
	}
</style>
