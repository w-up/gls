<template>
	<div id="bankcard">
		<mt-header fixed title="银行卡">
			<a slot="left">
				<mt-button icon="back" @click="back">返回</mt-button>
			</a>
		</mt-header>
		<div class="con-wrapper">
			<div class="rech_con">
				<h5 class="y_auton" v-if="useMsg.bank_user !== ''">您已绑定银行卡！</h5>
				<h5 class="w_auton" v-if="useMsg.bank_user == ''">请填写您的银行卡信息进行绑定！</h5>
				<div class="rech_list">
					<span>姓名：</span>
					<input v-if="useMsg.bank_user !== ''" type="text" v-model="useMsg.bank_user" name="" disabled="disabled" />
					<input v-if="useMsg.bank_user == ''" type="text" v-model="bank_user" name="" placeholder="请输入您的开户姓名" />
				</div>
				<div class="rech_list">
					<span>卡号：</span>
					<input v-if="useMsg.bank_card !== ''" type="text" v-model="useMsg.bank_card" name="" disabled="disabled" />
					<input v-if="useMsg.bank_card == ''" type="text"  v-model="bank_card" name="" placeholder="请输入银行卡号" />
				</div>
				<div class="rech_list">
					<span>开户行：</span>
					<input v-if="useMsg.bank_name !== ''" type="text" v-model="useMsg.bank_name" name="" disabled="disabled" />
					<input v-if="useMsg.bank_name == ''" type="text"  v-model="bank_name" name="" placeholder="请输入开户行全称" />
				</div>
				<div class="rech_list">
					<span>地址：</span>
					<input v-if="useMsg.bank_adderss !== ''" type="text" v-model="useMsg.bank_adderss" name="" disabled="disabled" />
					<input v-if="useMsg.bank_adderss == ''" type="text"  v-model="bank_adderss" name="" placeholder="请输入开户行地址" />
				</div>
				<mt-button v-if="useMsg.bank_adderss !== ''" type="default" @click="back">返回</mt-button>
				<mt-button v-if="useMsg.bank_adderss == ''" type="default" @click="bindBank">确认</mt-button>
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
				bank_user: '', //开户姓名
				bank_card: '', //银行卡号
				bank_name: '', //开户行名称
				bank_adderss: '', //开户行地址
				useMsg:[]//个人信息
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
			//绑定银行卡
			bindBank() {
				let that = this;
				if (!that.bank_user || that.bank_user == null) {
					Toast('请输入开户姓名');
				} else if (!that.bank_card || that.bank_card == null) {
	                  Toast('请输入银行卡号');
				} else if (!that.bank_name || that.bank_name == null) {
	                  Toast('请输入开户行名称');
				} else if (!that.bank_adderss || that.bank_adderss == null) {
	                  Toast('请输入开户行地址');
				} else {
					Indicator.open({
						text: "提交中...",
					});
					that
						.$http({
							url: "Personal/bankActive",
							method: "post",
							data: {
								token: window.sessionStorage.getItem('token'),
								bank_user: that.bank_user,
								bank_card: that.bank_card,
								bank_name: that.bank_name,
								bank_adderss: that.bank_adderss,
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
				}
	
			},
		}
	}
</script>

<style scoped="scoped">
	#bankcard {
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
		width: 22%;
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
