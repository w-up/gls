<template>
	<div id="wechat">
		<mt-header fixed title="微信">
			<a slot="left">
				<mt-button icon="back" @click="back">返回</mt-button>
			</a>
		</mt-header>
		<div class="con-wrapper">
			<div class="rech_con">
				<h5 class="y_auton" v-if="useMsg.wechat !== ''">您已绑定微信！</h5>
				<h5 class="w_auton" v-if="useMsg.wechat == ''">请填写您的微信号进行绑定！</h5>
				<div class="rech_list">
					<span>微信号：</span>
					<input v-if="useMsg.wechat !== ''" type="text" v-model="useMsg.wechat" name="" disabled="disabled" />
					<input v-if="useMsg.wechat == ''" type="text" v-model="wechat" name="" placeholder="请输入您的微信号" />
				</div>
				<mt-button  v-if="useMsg.wechat !== ''" type="default" @click="back">返回</mt-button>
				<mt-button  v-if="useMsg.wechat == ''" type="default" @click="subWechat">确认</mt-button>
			</div>
		</div>
	</div>
</template>

<script>
	import {
		Indicator,
		Toast
	} from 'mint-ui';
	
	export default {
		components: {
			Indicator,
			Toast
		},
		data() {
			return {
				wechat: '', //真实姓名
				useMsg:[]
			}
		},
		mounted: function() {
			let that = this;
			that.getUserMsg();
		},
		methods: {
			back() {
				this.$router.go(-1); //返回上一层
			},
			//获取实名认证信息
			getUserMsg() {
				let that = this;
				that
					.$http({
						url: "Personal/userMsg",
						method: "post",
						data: {
							token: window.sessionStorage.getItem('token'),
						}
					})
					.then(function(res) {
						if (res.data.code == 0) {
							//成功回调
							that.useMsg = res.data.data;
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
			//绑定微信
			subWechat() {
				let that = this;
				if (!that.wechat || that.wechat == null) {
					Toast('请输入真实姓名');
				} else {
					Indicator.open({
						text: "提交中...",
					});
					that
						.$http({
							url: "Personal/wechatActive",
							method: "post",
							data: {
								token: window.sessionStorage.getItem('token'),
								wechat: that.wechat,
							}
						})
						.then(function(res) {
							if (res.data.code == 0) {
								//成功回调
								Toast(res.data.msg);
								that.$router.go(-1);
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
	
			},
		}
	}
</script>

<style scoped="scoped">
	#wechat {
		width: 100%;
		height: 100%;
		overflow: hidden;
	}

	.con-wrapper {
		position: fixed;
		width: 100%;
		height: calc(100% - 40px);
		overflow-x: hidden;
		overflow-y: scroll;
		top: 40px;
	}

	.mint-header {
		position: relative;
		background: #EF6213;
	}

	.rech_con {
		width: 90%;
		margin: 0 auto;
		margin-top: 0.5rem;

	}
	 .rech_con h5{
		text-align: center;
		font-weight: normal;
		font-size: 0.26rem;
		padding-bottom: 0.1rem;
	}
	.rech_con h5.y_auton{
		color: #259B24;
	}
	.rech_con h5.w_auton{
		color: #EF6213;
	}

	.rech_con .rech_list {
		display: flex;
		align-items: center;
		padding: 0.2rem 0;
	}

	.rech_con .rech_list span {
		width: 24%;
		color: #333;
		font-size: 0.28rem;
	}
	.rech_con .rech_list input {
		flex: 1;
		height: 0.8rem;
		border: 1px solid #c8c8c8;
		border-radius: 0.1rem;
		padding-left: 0.1rem;
		background: #fff;
	}

	.rech_con .mint-button {
		position: relative;
		left: 50%;
		margin-left: -3rem;
		font-size: 0.3rem;
		width: 6rem;
		height: 0.8rem;
		color: #fff;
		margin-top: 0.3rem;
		border-radius: 0.2rem;
		background: #EF6213;
	}
</style>
