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
			<button type="primary">账号登录</button>
		</view>
	</view>
</template>

<script>
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
