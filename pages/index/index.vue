<template>
	<view class="content">
		<!-- money:金额  rbagmodelshow:红包弹框是否显示 options:红包参数设置 @onOpenRedBag:打开红包弹框事件 @onOpen：打开红包事件 @onConfirm：确认收款事件 -->
		<cc-red-bag :money="moneyStr" :rbagmodelshow="isShowModel" :options="redBagConfig" @onOpenRedBag="onOpenRedBag"
			@onOpen="onOpen" @onConfirm="onConfirm"></cc-red-bag>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				moneyStr: '',
				isShowModel: false,
				redBagConfig: {
					btnText: "确认收款",
				}
			}
		},
		onLoad() {


		},
		methods: {
			// 生成0-100随机金额 并保留两位小数
			createRandom() {

				// 生成0-100随机小数
				var min = 0;
				var max = 100;
				return (min + Math.random() * (max - min)).toFixed(2) + '';
			},

			//  打开红包弹框
			onOpenRedBag() {

				this.isShowModel = true;

			},

			// 打开红包
			onOpen() {
				// 生成随机金额
				this.moneyStr = this.createRandom();
			},

			// 点击确认收款按钮事件
			onConfirm(e) {

				console.log("点击确认收款按钮事件");
			},
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>