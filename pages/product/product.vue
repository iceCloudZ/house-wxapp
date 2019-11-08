<template>
	<view>
		<view class="container">
			<swiper class="swiper" :autoplay="true" :interval="3000" :duration="1000" :current="swiperCurrent" @change="changeSwiper">
				<swiper-item v-for="item in houseDetail.pictureList" :key="item.id">
					<image class="swiper-item" :src="item" mode="widthFix"></image>
				</swiper-item>
			</swiper>
			<!-- 轮播指示点样式修改 -->
			<view class="dots">
				<block v-for="(item,index) in houseDetail.pictureList.length" :key="item">
					<view class="dot" :class="index==swiperCurrent ? ' active' : ''"></view>
				</block>
			</view>
		</view>
		<view class="cell">
			<view class="cell-in">
				<text class="title">{{houseDetail.title}}</text>
			</view>
		</view>
		<view class="cell">
			<!-- 下边框 -->
			<view class="cell-in">
				<!-- 平铺 -->
				<view class="cell-flex">
					<view class="cell-flat">
						<text class="intro attr">售价</text>
						<text class="intro price">{{houseDetail.price}}万</text>
					</view>
					<view class="cell-flat">
						<text class="intro attr">房型</text>
						<text class="intro price">{{houseDetail.roomCount}}室{{houseDetail.hallCount}}厅{{houseDetail.bathCount}}卫</text>
					</view>
					<view class="cell-flat">
						<text class="intro attr">建筑面积</text>
						<text class="intro price">{{houseDetail.area/100}}m²</text>
					</view>
				</view>
			</view>
		</view>

		<view class="cell">
			<view class="cell-in-flex">
				<text class="label" v-for="v in houseDetail.labels" :key='v'>{{v}}</text>
			</view>
		</view>

		<view class="cell">
			<view class="cell-flex-text">
				<text class="description"><text class="grey-description">单价：</text>{{Math.floor(houseDetail.price *1000000 / houseDetail.area)}}元/平</text>
				<text class="description"><text class="grey-description">挂牌：</text>{{houseDetail.hangoutDate}}</text>
			</view>
			<view class="cell-flex-text">
				<text class="description"><text class="grey-description">朝向：</text>{{houseDetail.position}}</text>
				<text class="description"><text class="grey-description">楼层：</text>{{houseDetail.floor}}</text>
			</view>
			<view class="cell-flex-text">
				<text class="description"><text class="grey-description">楼型：</text>{{houseDetail.buildingType}}</text>
				<text class="description"><text class="grey-description">电梯：</text>{{houseDetail.elevator}}</text>
			</view>
			<view class="cell-flex-text">
				<text class="description"><text class="grey-description">装修：</text>{{houseDetail.renovation}}</text>
				<text class="description"><text class="grey-description">年代：</text>{{houseDetail.age}}年</text>
			</view>
			<view class="cell-flex-text">
				<text class="description"><text class="grey-description">用途：</text>{{houseDetail.purpose}}</text>
				<text class="description"><text class="grey-description">权属：</text>{{houseDetail.ownership}}</text>
			</view>
			<view class="cell-flex-text">
				<text class="description"><text class="grey-description">小区：</text>{{houseDetail.communityName}}</text>
			</view>
		</view>

		<view class="cell">
			<view class="cell-button">
				<text>咨询经纪人</text>
			</view>
		</view>
		
		<!-- TODO 推荐房源 -->
		<!-- TODO 房源地图 -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperImg: [
					'https://gd3.alicdn.com/imgextra/i3/0/O1CN01IiyFQI1UGShoFKt1O_!!0-item_pic.jpg_400x400.jpg',
					'https://gd3.alicdn.com/imgextra/i3/TB1RPFPPFXXXXcNXpXXXXXXXXXX_!!0-item_pic.jpg_400x400.jpg'
				],
				current: 0,
				swiperCurrent: 0,
				houseDetail: {}
			};
		},
		async onLoad(options) {

			//接收传值,id里面放的是标题，因为测试数据并没写id 
			let id = options.id;
			if (id) {
				this.$api.msg(`点击了${id}`);
			}
			this.loadData();
		},
		methods: {
			async loadData() {
				let houseDetail = await this.$api.json('houseDetail');
				this.houseDetail = houseDetail;
			},

			changeSwiper(e) {
				this.swiperCurrent = e.detail.current;
			}
		},
	}
</script>

<style lang='scss'>
	.container {
		position: relative;

		.swiper {
			width: 700upx;
			height: 500upx;
			border-radius: 16upx;
			overflow: hidden;
			margin-top: 16upx;
			position: relative;
			margin-left: 26upx;

			.swiper-item {
				width: 700upx;
				height: 500upx;
				border-radius: 16upx;
			}

		}

		.dots {
			position: absolute;
			bottom: 20upx;
			left: 50%;
			// 这里一定要注意兼容不然很可能踩坑
			transform: translate(-50%, 0);
			-webkit-transform: translate(-50%, 0);
			z-index: 99;
			display: flex;
			flex-direction: row;
			justify-content: center;

			.dot {
				width: 24upx;
				height: 8upx;
				transition: all .6s;
				background: rgba(0, 0, 0, .3);
				margin-right: 10upx;
			}

			.active {
				width: 24upx;
				height: 8upx;
				background: rgba(255, 255, 255, .8);
			}
		}
	}

	/* 标题简介 */
	.cell {
		padding: 0 30upx;
		
		.cell-button {
			display: flex;
			align-items:center;
			justify-content:center;
			margin: 10upx 0;
			padding: 20upx 0;
			background: rgba(220, 220, 220, 0.2);
			text {
				color: #4399FC;
			}
		}
		
		.cell-in-flex {
			display: flex;
			align-items: center;
			padding: 30upx 0 15upx 0;

			.label {
				float: left;
				display: inline;
				font-size: $font-sm;
				height: 40upx;
				line-height: 40upx;
				padding: 0 5upx;
				margin: 0 5upx;
				border-radius: 8upx;
			}

			.label:nth-child(1) {
				background: rgba(30, 144, 255, 0.2);
				color: rgba(30, 144, 255, 1);
			}

			.label:nth-child(2) {
				background: rgba(255, 99, 71, 0.2);
				color: rgba(255, 99, 71, 1);
			}

			.label:nth-child(3) {
				background: rgba(50, 205, 50, 0.2);
				color: rgba(50, 205, 50, 1);
			}
		}

		.cell-flex-text {
			display: flex;
			align-items: center;
			padding: 5upx 0;

			.grey-description {
				color: #909399;
			}

			.description {
				flex: 1 1 50%;
				font-size: $font-base + 3upx;
				color: black;
			}
		}

		.cell-in {
			border-bottom-width: 1upx;
			border-bottom-style: solid;
			border-bottom-color: #c8c7cc;

			.cell-flex {
				display: flex;
				align-items: center;

				.cell-flat {
					flex: 1 1 33.33%;
					padding: 40upx 0;
				}

				.attr {
					font-size: $font-sm + 3upx;
					color: $font-color-light;
				}

				.price {
					font-size: $font-base + 5upx;
					color: red;
				}

			}

			.title {
				font-size: 40upx;
				color: $font-color-dark;
				height: 50upx;
				line-height: 150upx;
				padding: 40upx 0;
			}

			.intro {
				display: block;
			}
		}
	}
</style>
