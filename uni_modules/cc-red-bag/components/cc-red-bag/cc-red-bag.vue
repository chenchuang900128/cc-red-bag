<!-- 活动红包 -->
<template>
	<view class="red-bag">
		<!-- 左侧红包 -->
		<image class="bag-btn" :animation="bagAnimation" src="../../image/bag.png" mode="scaleToFill"
			@click="rbagmodelshow = true"></image>

		<!-- 红包封面 -->
		<view v-if="rbagmodelshow" class="rbag-model" @touchmove.prevent.stop>
			<view class="rbag-con">
				<view class="rbag-box">
					<view class="rbag_top">
						<view class="rbag_top_info">
							<image v-if="config.userImg" class="rbag_logo" :src="config.userImg" mode="scaleToFill">
							</image>
							<image v-else class="rbag_logo" src="../../image/logo.jpg" mode="scaleToFill"></image>
							<view class="app_name">{{ config.userName }}</view>
							<view class="rbag_tips">{{ config.coverTitle }}</view>
						</view>
					</view>
					<view class="open_rbag_btn" :animation="openbrnanimation" @click="openBtn()">開</view>
				</view>
				<view class="hide_btn" @click.stop="onClose()">
					<icon type="cancel" color="#fbd977" size="32" />
				</view>
			</view>
		</view>

		<!-- 打开红包 -->
		<view v-if="openrbagmodelshow" class="open_rbag_model" @touchmove.prevent.stop>
			<image class="rbag_conbg" src="../../image/open_bag_bgImg.png"></image>
			<view class="rbag_conbg open_rbag_con">
				<view class="open_title">— {{ config.openTitle }} —</view>
				<view class="rbag_detail">
					<view class="open_money">
						<!-- 数字滚动 -->
						<view class="digital-scroll" :style="`font-size: 34px;color:#c95948;justify-content: center`">
							<view v-for="(item, index) in digitalData" :key="index"
								:class="{ 'digital': true, 'digital-str': isNaN(item.num) }">
								<!-- 符号显示 -->
								<view v-if="isNaN(item.num)">{{ item.num }}</view>
								<!-- 滚动的列表 -->
								<view v-else class="scroll-num">
									<view class="tra-num" :style="item.style">{{item.num}}</view>
								</view>
							</view>
						</view>

						<view class="danwei">元</view>
					</view>
					<view class="open_tips">{{ config.openTips }}</view>
				</view>
				<view class="lookbag_box">
					<view class="lookbag_btn">
						<view class="text" @click.stop="onConfirm()">{{ config.btnText }}</view>
					</view>
				</view>
				<view class="hide_btn" @click.stop="onClose()">
					<icon type="cancel" color="#fbd977" size="28" />
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			// 金额
			money: {
				type: [Number, String],
				default: '8.88',
				required: true
			},

			// 配置
			options: {
				type: Object,
				default: () => {}
			}
		},

		data() {
			return {
				defConfig: {
					userImg: '',
					userName: '前端组件开发',
					coverTitle: '恭喜发财',
					openTitle: '恭喜您获得',
					openTips: '已存入钱包，可直接提现',
					btnText: '查看我的钱包'
				}, // 默认配置
				bagAnimation: {}, // 固定小红包动画
				rbagmodelshow: false, // 红包封面
				openrbagmodelshow: false, // 拆封红包
				openbrnanimation: {}, // 拆封动画
				digitalData: [], // 滚动数字数据
			}
		},

		// onShow() {
		// 	this.setImageAnimation()
		// },

		computed: {
			// 处理配置项
			config() {
				const result = Object.assign(this.defConfig, this.options)
				return result
			}
		},

		onLoad() {
			setTimeout(() => {
				this.setImageAnimation()
			}, 2000);
		},

		methods: {
			// 侧边红包 => 动画
			setImageAnimation() {
				let next = true
				const animation = uni.createAnimation({
					duration: 1000,
					timingFunction: 'ease'
				})
				this.bagAnimation = animation
				setInterval(() => {
					const rotate = next ? 36 : 6
					animation.rotate(rotate).step()
					next = !next
					this.bagAnimation = animation.export()
				}, 1000)
			},

			// 红包封面 => 開红包按钮
			openBtn() {
				var animation = uni.createAnimation({
					duration: 1000,
					timingFunction: 'ease'
				})
				this.openbrnanimation = animation
				animation.rotateY(360).step()
				this.openbrnanimation = animation.export()
				setTimeout(() => {
					this.rbagmodelshow = false
					this.openrbagmodelshow = true
					this.openbrnanimation = {}
					this.setMoney()
					this.$emit('onCover') // 打开封面后回调
				}, 1000)
				
				this.$emit('onOpen') // 确认后回调
				
			},

			// 确认红包
			onConfirm() {
				this.openrbagmodelshow = false
				this.$emit('onConfirm') // 确认后回调
			},

			// 隐藏红包
			onClose() {
				this.rbagmodelshow = false
				this.openrbagmodelshow = false
				this.$emit('onClose') // 关闭后回调
			},

			// 设置金额
			setMoney() {
				const digitalArr = String(this.money).split('')
				const dataList = []
				digitalArr.forEach((num) => {
					const obj = {
						num: isNaN(num) ? num : Number(num),
						style: ''
					}
					dataList.push(obj)
				})
				this.digitalData = dataList
				this.setScrollNum()
			},

			// 滚动数字动画（重点）
			setScrollNum() {
				const defData = JSON.parse(JSON.stringify(this.digitalData))
				defData.forEach((item, index) => {
					// 设置移动距离
					 // item.style = `transform: translateY(-${item.num}em);`

					 
				})
				setTimeout(() => {
					this.digitalData = defData
				}, 20)
			}
		}
	}
</script>

<style lang="scss" scoped>
	@keyframes bagAni {
		0% {
			transform: rotate(6deg);
		}

		100% {
			transform: rotate(36deg);
		}
	}

	.red-bag {
		.bag-btn {
			position: fixed;
			left: -46rpx;
			top: 360rpx;
			width: 150rpx;
			height: 100rpx;
			z-index: 999;
			animation-name: bagAni;
			animation-duration: 1.1s;
			animation-iteration-count: infinite;
			animation-direction: alternate;
		}

		// 红包封面
		.rbag-model {
			position: fixed;
			top: 0;
			left: 0;
			width: 100%;
			height: 100vh;
			background-color: rgba(0, 0, 0, 0.3);
			display: flex;
			align-items: center;
			justify-content: center;
			z-index: 1000;

			.rbag-con {
				position: relative;
				width: 80%;
				height: 840rpx;
				background-color: #da4d44;
				border-radius: 14rpx;
				box-shadow: 0rpx 0rpx 10rpx rgba(0, 0, 0, 0.2);

				.rbag-box {
					position: relative;
					width: 100%;
					height: 100%;
					border-radius: 14rpx;
					overflow: hidden;
				}

				.rbag_top {
					position: absolute;
					left: -20%;
					top: 0;
					width: 140%;
					height: 540rpx;
					background-color: #e0534a;
					border-radius: 0 0 50% 50%;
					box-shadow: 0 0 14rpx rgba(0, 0, 0, 0.4);
					z-index: 1001;

					.rbag_top_info {
						margin-top: 60rpx;

						.rbag_logo {
							width: 160rpx;
							height: 160rpx;
							border-radius: 50%;
							display: block;
							margin: 0 auto;
							overflow: hidden;
						}

						.app_name {
							font-size: 38rpx;
							color: #f6ac96;
							text-align: center;
							margin-top: 18rpx;
							letter-spacing: 1rpx;
						}

						.rbag_tips {
							font-size: 50rpx;
							color: #edddd3;
							text-align: center;
							margin-top: 34rpx;
							letter-spacing: 1rpx;
						}
					}
				}

				.open_rbag_btn {
					position: absolute;
					top: 450rpx;
					left: 0;
					right: 0;
					width: 180rpx;
					height: 180rpx;
					line-height: 180rpx;
					border-radius: 50%;
					margin: 0 auto;
					text-align: center;
					background-color: #ffd287;
					font-size: 55rpx;
					color: #fef5e8;
					box-shadow: 2rpx 2rpx 6rpx rgba(0, 0, 0, 0.2);
					z-index: 1002;
				}

				.hide_btn {
					position: absolute;
					bottom: -110rpx;
					left: 0;
					right: 0;
					width: 90rpx;
					height: 90rpx;
					margin: 0 auto;
				}
			}
		}

		// 打开红包
		.open_rbag_model {
			position: fixed;
			top: 0;
			left: 0;
			width: 100%;
			height: 100vh;
			background-color: rgba(0, 0, 0, 0.3);
			z-index: 1000;

			.rbag_conbg {
				position: absolute;
				top: 0;
				left: 0;
				right: 0;
				bottom: 0;
				width: 80%;
				height: 840rpx;
				margin: auto;
				z-index: 1001;
			}

			.open_rbag_con {
				z-index: 1002;

				.open_title {
					height: 120rpx;
					line-height: 120rpx;
					text-align: center;
					font-size: 38rpx;
					letter-spacing: 2rpx;
					color: #e46965;
				}

				.rbag_detail {
					margin-top: 90rpx;

					.open_money {
						text-align: center;
						font-size: 80rpx;
						color: #c95948;
						font-weight: bold;
						display: flex;
						justify-content: center;

						.danwei {
							font-size: 30rpx;
							margin-left: 16rpx;
							margin-top: 24rpx;
						}
					}

					.open_tips {
						text-align: center;
						font-size: 30rpx;
						color: #d26762;
						margin-top: 30rpx;
					}
				}

				.lookbag_box {
					margin-top: 200rpx;
					display: flex;
					justify-content: center;

					.lookbag_btn {
						width: 70%;
						height: 90rpx;
						line-height: 90rpx;
						text-align: center;
						font-size: 32rpx;
						color: #c95948;
						letter-spacing: 2rpx;
						background-color: #ffd356;
						border-radius: 50rpx;
						box-shadow: 0rpx 0rpx 4rpx rgba(0, 0, 0, 0.2);
					}
				}

				.hide_btn {
					position: absolute;
					bottom: -110rpx;
					left: 0;
					right: 0;
					width: 80rpx;
					height: 80rpx;
					line-height: 80rpx;
					text-align: center;
					margin: 0 auto;
				}
			}
		}
	}

	.digital-scroll {
		font-size: 28rpx;
		font-weight: bold;
		display: flex;
		align-items: center;

		.digital {
			display: flex;
			justify-content: center;
			width: 0.7em; // 0.7em 是为了让文本间隔没那么宽
			height: 1em;
			line-height: 1em;
			overflow: hidden;

			.scroll-num {
				// 文本竖直排列
				writing-mode: vertical-rl;
				text-orientation: upright;

				.tra-num {
					transition: all 1s;
				}
			}
		}

		.digital-str {
			width: auto;
			line-height: 1em;
		}
	}
</style>