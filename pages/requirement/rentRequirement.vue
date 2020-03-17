<template>
	<view class="content">
		<view class="row b-b">
			<text class="tit">您的姓名</text>
			<input class="input" type="text" v-model="profileRequirement.profileName" placeholder="请输入姓名" placeholder-class="placeholder" />
		</view>
		<view class="row b-b">
			<text class="tit">手机号</text>
			<input class="input" type="number" v-model="profileRequirement.profilePhone" placeholder="请输入手机号码(必填)" placeholder-class="placeholder" />
		</view>
		<view class="row b-b">
			<text class="tit">地址</text>
			<text @click="chooseLocation" class="input">
				{{profileRequirement.addressName}}
			</text>
			<text class="yticon icon-shouhuodizhi"></text>
		</view>
		<view class="row b-b">
			<text class="tit">门牌号</text>
			<input class="input" type="text" v-model="profileRequirement.addressReal" placeholder="楼号、门牌(可不填)" placeholder-class="placeholder" />
		</view>
		<view class="row b-b">
			<text class="tit">大概租金</text>
			<input class="input" type="text" v-model="profileRequirement.price" placeholder="单位:元/月" placeholder-class="placeholder" />
		</view>
		<view class="row b-b">
			<text class="tit">大概面积</text>
			<input class="input" type="text" v-model="profileRequirement.area" placeholder="单位:平方米" placeholder-class="placeholder" />
		</view>
		
		<view class="row default-row">
			<text class="tit">有房通知</text>
			<switch :checked="profileRequirement.notify" color="#fa436a" @change="switchChange" />
		</view>
		<button class="add-btn" @click="confirm">提交</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				profileRequirement: {					
					addressName: '在地图选择',
					addressReal: '',
					area: 0,
					commentAddress: '',
					notify: true,
					price: 0,
					profileId: 0,
					profileName: '',
					profilePhone: '',
					requirementType: 'RENT'
				}
			}
		},
		onLoad(option){
			title = '我要租房,完善以下信息'
			this.manageType = option.type;
			uni.setNavigationBarTitle({
				title
			})
			// 获取用户id 与 手机号
			this.profileRequirement.profileId = uni.getStorageSync('profileId');
			this.profileRequirement.profilePhone = uni.getStorageSync('profilePhone');
		},
		methods: {
			switchChange(e){
				this.profileRequirement.notify = e.detail;
			},
			
			//地图选择地址
			chooseLocation(){
				uni.chooseLocation({
					success: (data)=> {
						console.log(data)
						this.profileRequirement.addressName = data.name;
						this.profileRequirement.addressReal = data.address;
					}
				})
			},
			
			//提交
			confirm(){
				wx.requestSubscribeMessage({
				  tmplIds: ['44NNQHSAGal4JNtolVAPtYnjjh0Mzi3YjPuXs5JPDQo'],
				  success (response) { 
					  console.log(response);
				  }
				})
				
				let data = this.profileRequirement;
				if(!data.profileName){
					this.$api.msg('请填写您的姓名');
					return;
				}
				if(!/(^1[3|4|5|7|8][0-9]{9}$)/.test(data.profilePhone)){
					this.$api.msg('请输入正确的手机号码');
					return;
				}
				if(!data.addressReal){
					this.$api.msg('请在地图选择所在位置');
					return;
				}
				
				// 新增用户需求
				uni.request({
					url: 'https://api.icecloudz.cn/profileRequirements',
					method: 'POST',
					data: this.profileRequirement,
					success: (res) => {
						console.log(res);
					}
				})
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: $page-color-base;
		padding-top: 16upx;
	}

	.row{
		display: flex;
		align-items: center;
		position: relative;
		padding:0 30upx;
		height: 110upx;
		background: #fff;
		
		.tit{
			flex-shrink: 0;
			width: 160upx;
			font-size: 30upx;
			color: $font-color-dark;
		}
		.input{
			flex: 1;
			font-size: 30upx;
			color: $font-color-dark;
		}
		.icon-shouhuodizhi{
			font-size: 36upx;
			color: $font-color-light;
		}
	}
	.default-row{
		margin-top: 16upx;
		.tit{
			flex: 1;
		}
		switch{
			transform: translateX(16upx) scale(.9);
		}
	}
	.add-btn{
		display: flex;
		align-items: center;
		justify-content: center;
		width: 690upx;
		height: 80upx;
		margin: 60upx auto;
		font-size: $font-lg;
		color: #fff;
		background-color: $base-color;
		border-radius: 10upx;
		box-shadow: 1px 2px 5px rgba(219, 63, 96, 0.4);
	}
</style>
