<template>
	<view class="parent">
		<view class="line-parent">
			<view class="left"></view>
			<view class="mid">
				<label>推荐方式登录</label>
			</view>
			<view class="right"></view>
		</view>
		<view class="cell">
			<button type="primary" open-type="getPhoneNumber" @getphonenumber="getPhoneNumber">微信账号登录</button>
		</view>
	</view>
</template>

<script>
	export default {
		components: {},
		data() {
			return {};
		},
		onLoad() {

		},
		methods: {
			login() {
				wx.login({
					success(res) {
						if (res.code) { //发起网络请求 
							wx.request({
								url: 'https://api.icecloudz.cn/wx/user/login',
								data: {
									code: res.code
								},
								success(e) {
									console.log(e.data);
									wx.setStorage({
										key: "sessionKey",
										data: e.data.sessionKey
									});
								}
							})
						} else {
							console.log('登录失败！' + res.errMsg)
						}
					}
				})
			},


			getPhoneNumber(e) {

				wx.request({
					url: 'https://api.icecloudz.cn/wx/user/phone',
					data: {
						sessionKey: uni.getStorageSync('sessionKey'),
						iv: e.detail.iv,
						encryptedData: e.detail.encryptedData
					},
					success(res) {
						wx.setStorageSync('userPhone',res.data.phoneNumber)
						console.log(uni.getStorageSync('userPhone'))
					}
				})

			},
		}
	}
</script>

<style lang="scss">
	.parent {
		display: flex;
		flex-direction: column;

		.cell {
			padding: 80upx 30upx;
		}

		.line-parent {
			display: flex;
			padding: 0 30upx;
			height: 150upx;

			.left {
				border-bottom-width: 1upx;
				border-bottom-style: solid;
				border-bottom-color: #c8c7cc;
				flex: 0 1 35%;
			}

			.mid {
				flex: 0 2 30%;

				label {
					display: block;
					padding-left: 35upx;
					padding-top: 135upx;
					font-size: $font-sm;
					color: $font-color-light;
				}

			}

			.right {
				border-bottom-width: 1upx;
				border-bottom-style: solid;
				border-bottom-color: #c8c7cc;
				flex: 0 1 35%;
			}
		}
	}
</style>
