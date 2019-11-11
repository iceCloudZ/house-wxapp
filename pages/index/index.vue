<template>
	<view class="container">
		<!-- 小程序头部兼容 -->
		<!-- #ifdef MP -->
		<view class="mp-search-box">
			<input class="ser-input" type="text" value="输入关键字搜索" disabled />
		</view>
		<!-- #endif -->

		<!-- 头部轮播 -->
		<view class="carousel-section">
			<!-- 标题栏和状态栏占位符 -->
			<view class="titleNview-placing"></view>
			<!-- 背景色区域 -->
			<view class="titleNview-background" :style="{backgroundColor:titleNViewBackground}"></view>
			<swiper class="carousel" circular @change="swiperChange">
				<swiper-item v-for="(item, index) in carouselList" :key="index" class="carousel-item" @click="navToDetailPage({title: '轮播广告'})">
					<image :src="item.src" />
				</swiper-item>
			</swiper>
		</view>
		<!-- 分类 -->
		<view class="cate-section">
			<view class="cate-item" @click="navToRequirement('rent')">
				<image src="/static/temp/fangzu.png"></image>
				<text>我要租房</text>
			</view>
			<view class="cate-item" @click="navToRequirement('buy')">
				<image src="/static/temp/baofang.png"></image>
				<text>我要买房</text>
			</view>
			<view class="cate-item">
				<image src="/static/temp/tianxie.png"></image>
				<text>登记房源</text>
			</view>
			<view class="cate-item">
				<image src="/static/temp/dianhualianxi.png"></image>
				<text>联系我们</text>
			</view>
		</view>

		<!-- 列表 -->
		<view class="cart-list">
			<block v-for="(item, index) in cartList" :key="item.id">
				<view class="cart-item" :class="{'b-b': index!==cartList.length-1}">
					<view class="image-wrapper">
						<image :src="item.firstPic" :class="[item.loaded]" mode="aspectFill" lazy-load @load="onImageLoad('cartList', index)"
						 @error="onImageError('cartList', index)" @click="navToDetailPage({title: '轮播广告'})"></image>
					</view>
					<view class="item-right">
						<text class="clamp title">{{item.title}}</text>
						<text class="attr">{{item.roomCount}}室{{item.hallCount}}厅/{{item.area/100}}m²/{{item.position}}/{{item.communityName}}</text>
						<view class="flexBox">
							<text class="label" v-for="v in item.labels" :key='v'>{{v}}</text>
						</view>
						<view class="flexBox">
							<text class="price">{{item.price}}万 </text>
							<text class="attr">{{Math.floor(item.price *1000000 / item.area)}}元/平</text>
						</view>
					</view>
				</view>
			</block>
		</view>
		<view class="cell">
			<view class="cell-button" >
				<!-- <text>登陆</text> -->
				<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber">登陆</button>
			</view>
		</view>
<!-- 		<view class="cell">
			<button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber"></button>
		</view> -->

	</view>
</template>

<script>
	export default {

		data() {
			return {
				titleNViewBackground: '',
				swiperCurrent: 0,
				swiperLength: 0,
				carouselList: [],
				goodsList: [],
				cartList: [],
			};
		},

		onLoad() {
			this.loadData();
			this.login();
		},
		methods: {

			login() {
				wx.login({
					success(res) {
						if (res.code) { //发起网络请求 
							wx.request({
								url: 'https://api.icecloudz.cn/huser/wx/user/login',
								data: {
									code: res.code
								},
								success(e) {
									console.log(e.data);
									wx.setStorage({
										  key:"sessionKey",
										  data:e.data.sessionKey
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
				console.log(e.detail.iv)
				console.log(e.detail.encryptedData)
				
				wx.request({
					url: 'https://api.icecloudz.cn/huser/wx/user/phone',
					data: {
						sessionKey: wx.getStorageSync('sessionKey'),
						iv: e.detail.iv,
						encryptedData: e.detail.encryptedData
					},
					success(data) {
						console.log(data);
					}
				})
				
			},

			// 跳转到用户需求页
			navToRequirement(type) {
				uni.navigateTo({
					url: `/pages/address/addressManage?type=${type}`
				})
			},

			/**
			 * 请求静态数据只是为了代码不那么乱
			 * 分次请求未作整合
			 */
			async loadData() {
				let carouselList = await this.$api.json('carouselList');
				this.titleNViewBackground = carouselList[0].background;
				this.swiperLength = carouselList.length;
				this.carouselList = carouselList;

				let goodsList = await this.$api.json('goodsList');
				this.goodsList = goodsList || [];

				let list = await this.$api.json('cartList');
				let cartList = list.map(item => {
					item.checked = true;
					return item;
				});
				this.cartList = cartList;
			},
			//轮播图切换修改背景色
			swiperChange(e) {
				const index = e.detail.current;
				this.swiperCurrent = index;
				this.titleNViewBackground = this.carouselList[index].background;
			},
			//详情页
			navToDetailPage(item) {
				//测试数据没有写id，用title代替
				let id = item.title;
				uni.navigateTo({
					url: `/pages/product/product?id=${id}`
				})
			},
			//监听image加载完成
			onImageLoad(key, index) {
				this.$set(this[key][index], 'loaded', 'loaded');
			},
			//监听image加载失败
			onImageError(key, index) {
				this[key][index].image = '/static/errorImage.jpg';
			},
		},
		// #ifndef MP
		// 标题栏input搜索框点击
		onNavigationBarSearchInputClicked: async function(e) {
			this.$api.msg('点击了搜索框');
		},
		//点击导航栏 buttons 时触发
		onNavigationBarButtonTap(e) {
			const index = e.index;
			if (index === 0) {
				this.$api.msg('点击了扫描');
			} else if (index === 1) {
				// #ifdef APP-PLUS
				const pages = getCurrentPages();
				const page = pages[pages.length - 1];
				const currentWebview = page.$getAppWebview();
				currentWebview.hideTitleNViewButtonRedDot({
					index
				});
				// #endif
				uni.navigateTo({
					url: '/pages/notice/notice'
				})
			}
		}
		// #endif
	}
</script>

<style lang="scss">
	/* #ifdef MP */
	.mp-search-box {
		position: absolute;
		left: 0;
		top: 30upx;
		z-index: 9999;
		width: 100%;
		padding: 0 80upx;

		.ser-input {
			flex: 1;
			height: 56upx;
			line-height: 56upx;
			text-align: center;
			font-size: 28upx;
			color: $font-color-base;
			border-radius: 20px;
			background: rgba(255, 255, 255, .6);
		}
	}

	page {
		.cate-section {
			position: relative;
			z-index: 5;
			border-radius: 16upx 16upx 0 0;
			margin-top: -20upx;
		}

		.carousel-section {
			padding: 0;

			.titleNview-placing {
				padding-top: 0;
				height: 0;
			}

			.carousel {
				.carousel-item {
					padding: 0;
				}
			}

			.swiper-dots {
				left: 45upx;
				bottom: 40upx;
			}
		}
	}

	/* #endif */


	page {
		background: #f5f5f5;
	}

	.m-t {
		margin-top: 16upx;
	}

	/* 头部 轮播图 */
	.carousel-section {
		position: relative;
		padding-top: 10px;

		.titleNview-placing {
			height: var(--status-bar-height);
			padding-top: 44px;
			box-sizing: content-box;
		}

		.titleNview-background {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 426upx;
			transition: .4s;
		}
	}

	.carousel {
		width: 100%;
		height: 350upx;

		.carousel-item {
			width: 100%;
			height: 100%;
			padding: 0 28upx;
			overflow: hidden;
		}

		image {
			width: 100%;
			height: 100%;
			border-radius: 10upx;
		}
	}

	.swiper-dots {
		display: flex;
		position: absolute;
		left: 60upx;
		bottom: 15upx;
		width: 72upx;
		height: 36upx;
		background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAABkCAYAAADDhn8LAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTMyIDc5LjE1OTI4NCwgMjAxNi8wNC8xOS0xMzoxMzo0MCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OTk4MzlBNjE0NjU1MTFFOUExNjRFQ0I3RTQ0NEExQjMiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OTk4MzlBNjA0NjU1MTFFOUExNjRFQ0I3RTQ0NEExQjMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6Q0E3RUNERkE0NjExMTFFOTg5NzI4MTM2Rjg0OUQwOEUiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6Q0E3RUNERkI0NjExMTFFOTg5NzI4MTM2Rjg0OUQwOEUiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz4Gh5BPAAACTUlEQVR42uzcQW7jQAwFUdN306l1uWwNww5kqdsmm6/2MwtVCp8CosQtP9vg/2+/gY+DRAMBgqnjIp2PaCxCLLldpPARRIiFj1yBbMV+cHZh9PURRLQNhY8kgWyL/WDtwujjI8hoE8rKLqb5CDJaRMJHokC6yKgSCR9JAukmokIknCQJpLOIrJFwMsBJELFcKHwM9BFkLBMKFxNcBCHlQ+FhoocgpVwwnv0Xn30QBJGMC0QcaBVJiAMiec/dcwKuL4j1QMsVCXFAJE4s4NQA3K/8Y6DzO4g40P7UcmIBJxbEesCKWBDg8wWxHrAiFgT4fEGsB/CwIhYE+AeBAAdPLOcV8HRmWRDAiQVcO7GcV8CLM8uCAE4sQCDAlHcQ7x+ABQEEAggEEAggEEAggEAAgQACASAQQCCAQACBAAIBBAIIBBAIIBBAIABe4e9iAe/xd7EAJxYgEGDeO4j3EODp/cOCAE4sYMyJ5cwCHs4rCwI4sYBxJ5YzC84rCwKcXxArAuthQYDzC2JF0H49LAhwYUGsCFqvx5EF2T07dMaJBetx4cRyaqFtHJ8EIhK0i8OJBQxcECuCVutxJhCRoE0cZwMRyRcFefa/ffZBVPogePihhyCnbBhcfMFFEFM+DD4m+ghSlgmDkwlOgpAl4+BkkJMgZdk4+EgaSCcpVX7bmY9kgXQQU+1TgE0c+QJZUUz1b2T4SBbIKmJW+3iMj2SBVBWz+leVfCQLpIqYbp8b85EskIxyfIOfK5Sf+wiCRJEsllQ+oqEkQfBxmD8BBgA5hVjXyrBNUQAAAABJRU5ErkJggg==);
		background-size: 100% 100%;

		.num {
			width: 36upx;
			height: 36upx;
			border-radius: 50px;
			font-size: 24upx;
			color: #fff;
			text-align: center;
			line-height: 36upx;
		}

		.sign {
			position: absolute;
			top: 0;
			left: 50%;
			line-height: 36upx;
			font-size: 12upx;
			color: #fff;
			transform: translateX(-50%);
		}
	}

	/* 分类 */
	.cate-section {
		display: flex;
		justify-content: space-around;
		align-items: center;
		flex-wrap: wrap;
		padding: 30upx 22upx;
		background: #fff;

		.cate-item {
			display: flex;
			flex-direction: column;
			align-items: center;
			font-size: $font-sm + 2upx;
			color: $font-color-dark;
		}

		/* 原图标颜色太深,不想改图了,所以加了透明度 */
		image {
			width: 88upx;
			height: 88upx;
			margin-bottom: 14upx;
			border-radius: 50%;
			opacity: .7;
		}
	}

	.cart-item {
		display: flex;
		position: relative;
		padding: 30upx 40upx;
		background: #fff;

		.image-wrapper {
			width: 230upx;
			height: 230upx;
			flex-shrink: 0;
			position: relative;

			image {
				border-radius: 8upx;
			}
		}

		.checkbox {
			position: absolute;
			left: -16upx;
			top: -16upx;
			z-index: 8;
			font-size: 44upx;
			line-height: 1;
			padding: 4upx;
			color: $font-color-disabled;
			background: #fff;
			border-radius: 50px;
		}

		.item-right {
			display: flex;
			flex-direction: column;
			flex: 1;
			overflow: hidden;
			position: relative;
			padding-left: 30upx;

			.title {
				font-size: $font-base + 4upx;
				color: $font-color-dark;
				height: 60upx;
				line-height: 60upx;
				word-break: keep-all;
				word-wrap: break-word;
			}

			.attr {
				font-size: $font-sm + 2upx;
				color: $font-color-light;
				height: 60upx;
				line-height: 60upx;
			}

			.flexBox {
				display: flex;
			}

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

			.price {
				font-size: $font-base + 10upx;
				color: red;
				height: 60upx;
				line-height: 60upx;
			}
		}

		.del-btn {
			padding: 4upx 10upx;
			font-size: 34upx;
			height: 50upx;
			color: $font-color-light;
		}
	}

	.container {
		background: white;
	}

	.cell {
		padding: 0 30upx;

		.cell-button {
			display: flex;
			align-items: center;
			justify-content: center;
			margin: 10upx 0;
			padding: 20upx 0;
			background: rgba(220, 220, 220, 0.2);

			text {
				color: #4399FC;
			}
		}

		.cell-fix {
			margin: 10upx 0;
			padding: 20upx 0;
		}
	}
</style>
