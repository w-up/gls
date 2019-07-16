<template>
	<div id="yipinyidi">
		<mt-header fixed title="一品一地">
			<a slot="left">
				<mt-button icon="back" @click="back">返回</mt-button>
			</a>
			<p slot="right" @click="gotoRecord">记录</p>
		</mt-header>
		<div class="con-wrapper">
			
			<div class="yipin_se">
				<select class="selectpicker" v-model="area_id" @change="selectChange($event)">
					<option v-for="(areaitem, index) in areaList" :key="index" :value="areaitem.id">{{areaitem.name}}</option>
				</select>
				<div class="search">
					<input type="search" v-model="name" name="" placeholder="请输入名称进行搜索" />
					<span class="iconfont icon-tabsearch" @click="search"></span>
				</div>
			</div>
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
			<div class="yi_content">
				<div class="yipin">
					<div class="yipin_list" v-for="(subitem,index) in subscribe" :key="index">
						<div class="yipin_img">
							<img :src="subitem.img" />
						</div>
						<div class="yipin_con">
							<div class="yipin_title">
								<h4>{{subitem.title}}</h4>
								<button class="yi_btn" type="primary" @click="reserve(subitem.id,subitem.number)">预定</button>
							</div>
							<div class="mian">
								<div class="spend">
									<span>元/斤</span>
									<span>{{subitem.spend}}</span>
									<span>数量 1</span>
								</div>
								<div class="stepp">
									<van-stepper v-model="subitem.number" />
								</div>
							</div>
							<h5>出售时间：{{subitem.sell_time}}</h5>
							
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
				name: '', //搜索名称
				subscribe: [], //一品一地数据,
				area_id: 1, //默认地址id
				areaList: [], //地址列表
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
			that.getSubscribe(); //
			that.getAreaList();
		},
		methods: {
			back() {
				this.$router.go(-1); //返回上一层
			},
			gotoRecord() {
				this.$router.push({
					path: "/reserecord"
				});
			},
			 //下拉刷新
			onRefresh() {
			  let that = this;
			  that.isLoading = true;
			  that.loading = false;
			  that.nif = false;
			  that.pageindex = 1;
			  that.subscribe = [];
			  that.getSubscribe(1);
			},
			//上拉加载更多
			loadMore() {
			  let that = this;
			  that.lif = true;
			  that.pageindex++;
			  that.getSubscribe(0);
			},
			//选择区域
			selectChange(i) {
				let that = this;
					that.area_id = Number(i.target.value);
					that.loading = false;
					that.nif = false;
					that.pageindex = 1;
					that.subscribe = [];
					that.getSubscribe(1);
			},
			//获取一品一地列表
			getSubscribe(i) {
				let that = this;
				 if (i) {
				  that.lif = true;
				}
				Indicator.open({
					text: "加载中...",
				});
				that
					.$http({
						url: "Subscribe/index",
						method: "post",
						data: {
							area_id: that.area_id,
							name: that.name,
						    p: that.pageindex
						}
					})
					.then(function(res) {
						 that.lif = false;
						that.isLoading = false;
						if (res.data.code == 0) {
							 //成功回调
							if (res.data.data.list != "") {
							  that.subscribe = that.subscribe.concat(res.data.data.list);
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
			//搜索
			search() {
				let that = this;									
					that.loading = false;
					that.nif = false;
					that.pageindex = 1;
					that.subscribe = [];	
					that.getSubscribe(1);			
			},
			//获取地址列表
			getAreaList() {
				let that = this;
				that
					.$http({
						url: "Register/areaList",
						method: "post",
					})
					.then(function(res) {
						if (res.data.code == 0) {
							//成功回调
							that.areaList = res.data.data;
						} else {
							//失败
							Toast(res.data.msg);
						}
					})
					.catch(function(error) {
						Toast({
							message: "网络连接失败",
							position: "bottom",
							duration: 5000
						});
					});
			},
			//预定
			reserve(id, number) {
				let that = this;
				Indicator.open({
					text: "提交中...",
				});
				that
					.$http({
						url: "Subscribe/subscribeActive",
						method: "post",
						data: {
							token: window.sessionStorage.getItem('token'),
							id: id,
							number: number
						}
					})
					.then(function(res) {
						if (res.data.code == 0) {
							//成功回调
							Toast(res.data.msg);
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
	#yipinyidi {
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
      width: 90%;
	  height: 100%;
      margin: 0 auto;
      margin-top: 0.1rem;
    }
	.yipin_se {
		display: flex;
		width: 90%;
		margin: 0 auto;
		padding: 0.2rem 0;
		position: relative;
	}

	.yipin_se select {
		width: 22%;
		height: 0.7rem;
		border: 1px solid #C8C8C8;
		border-radius: 0.1rem;
		color: #333;
	}

	.yipin_se .search {
		width: 76%;
	}

	.yipin_se .search input[type=search]::-webkit-search-cancel-button {
		-webkit-appearance: none;
	}

	.yipin_se .search input {
		margin-left: 0.2rem;
		width: 100%;
		height: 0.7rem;
		font-size: 0.26rem;
		border: none;
		border: 1px solid #C8C8C8;
		border-radius: 0.1rem;
		padding-left: 0.1rem;
	}

	.yipin_se .search span {
		position: absolute;
		top: 0.4rem;
		right: 0.1rem;
		color: #EF6213;
	}

	.yipin_list {
		display: flex;
		margin: 0.2rem 0;
		height: 1.8rem;
		align-items: center;
	}

	.yipin_list .yipin_img {
		width: 26%;
		height: 90%;

	}

	.yipin_list .yipin_img img {
		width: 100%;
		height: 100%;
	}

	.yipin_list .yipin_con {
		margin-left: 0.2rem;
		flex: 1;
	}

	.yipin_list .yipin_con .yipin_title h4 {
		font-size: 0.28rem;
		color: #333;
	}
    .yipin_list .yipin_con .yipin_title{
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
	.yipin_list .yipin_con h5 {
		font-weight: normal;
		/* line-height: 0.6rem; */
		color: #555;
	}

	.yipin_list .yipin_con .mian {
		padding: 0.1rem 0;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.yipin_list .yipin_con .mian span {
		font-size: 0.26rem;
		color: #666;
	}

	.yipin_list .yipin_con .mian span:nth-child(2) {
		padding-right: 0.3rem;
		color: #ef6213;
	}

	.yipin_list .yipin_con .qixian {
		/* margin-top: 2px; */
		display: flex;
		justify-content: flex-end;
	}
  .yipin_list .yipin_con .yi_btn{
	  width: 1rem;
	  height: 0.5rem;
	  border: none;
	  background: #EF6213;
	  color: #fff;
	  font-size: 0.26rem;
	  border-radius: 0.1rem;
  }
	.yipin_list .mint-button--normal {
		/* height: 0.8rem; */
		padding: 0 0.4rem;
	}
	/* 暂无数据 */
	#no-data {
		width: 100%;
		text-align: center;
		padding-top: 2rem;
		transition: all 0.2s;
	}
	
	#no-data>img {
		width: 40%;
		height: auto;
	}
</style>
