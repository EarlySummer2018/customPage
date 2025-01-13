<template>
	<view
		class="com-health-box"
		:style="{
			padding: padStyle,
			backgroundColor: colorToRgba(curStyle.bgColor, curStyle.opacity)
		}"
	>
		<template v-if="healthData.is_show">
			<view
				class="com-health"
				:style="healthStyle"
			>
				<view class="health-userinfo">
					<view
						class="avatar"
						:style="{
							backgroundImage: `url(${
								healthData.headimg || defaultHeadimg
							})`
						}"
					></view>
					<view class="name-day">
						<view class="name">
							<text class="max" style="margin-right: 10rpx">
								{{ healthData.username || '未登录' }}
							</text>
							<text v-if="healthData.birthday">
								({{ healthData.birthday }})
							</text>
						</view>
						<view>加入{{ healthData.join_time || 0 }}天</view>
					</view>
					<view
						:style="{ color: curStyle.btnColor }"
						class="base-info-btn"
						@click="toViewInfo()"
					>
						基本信息
					</view>
				</view>
				<view
					class="health-info"
					:style="{
						backgroundImage: `url(${curOptions.contentImg.url})`,
						backgroundColor: `#FFFFFF`
					}"
				>
					<view class="weight-box">
						<view class="item start-weight">
							<view class="num">
								<template v-if="healthData.initial_weight">
									<text class="max">
										{{ healthData.initial_weight }}
									</text>
									<text class="unit">
										斤
									</text>
								</template>
								<text class="max" v-else>--</text>
							</view>
							<view class="desc">初始体重</view>
						</view>
						<view class="item current-weight" @click="toCheckData()">
							<view class="num">
								<template v-if="healthData.latest_weight">
									<text
										class="max"
										:style="{
											color: renovationData.button_color
										}"
									>
										{{ healthData.latest_weight | kg2Jin }}
									</text>
									<text
										:style="{
											color: renovationData.button_color
										}"
										class="unit"
									>
										斤
									</text>
								</template>
								<text
									class="max"
									:style="{
										color: renovationData.button_color
									}"
									v-else
								>
									--
								</text>
								
								<view class="line" :style="{
										'background-color': renovationData.button_color
									}" v-if="healthData.latest_weight">
									
								</view>
							</view>
							<view class="desc">最新上秤</view>
							<!-- <view class="icon bg-cover">
							
							</view> -->
						</view>
						<view class="item target-weirht">
							<view class="num">
								<template v-if="healthData.goal_weight">
									<text class="max">
										{{ healthData.goal_weight }}
									</text>
									<text class="unit" >
										斤
									</text>
								</template>
								<text class="max" v-else>--</text>
							</view>
							<view class="desc">目标体重</view>
						</view>
					</view>
					<view class="card">
						<view
							class="card-item"
							:style="{
								background: colorToRgba(
									curStyle.btnColor,
									0.2
								)
							}"
							@click="toUrl('/indicatorInfo/indicatorInfo')"
						>
							<view>{{ healthData.bmi || '--' }}</view>
							<view class="desc">BMI</view>
						</view>

						<view
							class="card-item"
							:style="{
								background: colorToRgba(
									curStyle.btnColor,
									0.2
								)
							}"
							@click="toUrl('/indicatorInfo/indicatorInfo')"
						>
							<view>
								<text>{{ healthData.moisture || '--' }}</text>
								<text v-if="healthData.moisture">%</text>
							</view>
							<view class="desc">水分</view>
						</view>
						<view
							class="card-item"
							:style="{
								background: colorToRgba(
									curStyle.btnColor,
									0.2
								)
							}"
							@click="toUrl('/indicatorInfo/indicatorInfo')"
						>
							<view>
								<text>{{ healthData.fat || '--' }}</text>
								<text v-if="healthData.fat">%</text>
							</view>
							<view class="desc">体脂率</view>
						</view>
						<view
							class="card-item long"
							:style="{
								backgroundColor: renovationData.button_color
							}"
							@click="toNextPage()"
						>
							<text>去上秤</text>
						</view>
					</view>
				</view>
			</view>
		</template>
	</view>
</template>

<script>
	import { mapState } from 'vuex'
	import {
		colorToRgba,
		transformPixel,
		jumpLink
	} from '@/components/template/utils.js'
	export default {
		name: 'ComHealth',
		props: {
			curData: {
				type: Object,
				default() {
					return {}
				}
			}
		},
		data() {
			return {
				padStyle: '',
				healthStyle: '',
				healthData: {
					latest_weight: '', //体重
					bmi: null, //BMI
					username: '', //用户名
					join_time: '', //加入天数
					initial_weight: '', //初始体重
					goal_weight: '', //目标体重
					fat: null, //体脂率
					moisture: null, //水分率
					is_show: 1
				},
				base_setting: {},
				curStyle: {},
				curOptions: {}
			}
		},
		watch: {
			isChangeBody(nVal) {
				if (nVal) {
					this.gethealthData()
				}
			},
			userStatus(nVal) {
				if(nVal && nVal.is_user) {
					this.gethealthData()
				}
			}
		},
		computed: {
			isChangeBody() {
				return this.$store.state.isChangeBody
			},
			userStatus() {
				return this.$store.state.status
			},
			...mapState({
				status: (state) => state.status,
				renovationData: (state) => state.renovationData
			})
		},
		created() {
			this.curStyle = this.curData.style || {}
			this.curOptions = this.curData.options || {}
			this.setPadding()
			this.setHealthStyle()
			if (this.status.is_user) {
				this.gethealthData()
			}
		},
		methods: {
			transformPixel,
			colorToRgba,

			toCheckInfo() {
				if (!this.status.is_user) {
					uni.navigateTo({
						url: `/pages/login/login?callback=${this.getCurrentPageRoute()}`
					})
					return
				}
				uni.navigateTo({
					url: '/indicatorInfo/indicatorInfo'
				})
			},
			toCheckData() {
				if (!this.status.is_user) {
					uni.navigateTo({
						url: `/pages/login/login?callback=${this.getCurrentPageRoute()}`
					})
					return
				}
				uni.navigateTo({
					url: '/health/health-data'
				})
			},

			toViewInfo() {
				if (!this.status.is_user) {
					uni.navigateTo({
						url: `/pages/login/login?callback=${this.getCurrentPageRoute()}`
					})
					return
				}
				uni.navigateTo({
					url: '/health/improve-info?type=1'
				})
			},
			toNextPage() {
				if (!this.status.is_user) {
					uni.navigateTo({
						url: `/pages/login/login?callback=${this.getCurrentPageRoute()}`
					})
					return
				}
				const { initial_weight, birthday } = this.healthData
				if (!initial_weight || !birthday) {
					uni.navigateTo({
						url: '/health/improve-info'
					})
				} else if (!this.healthData.deviceld) {
					uni.navigateTo({
						url: '/health/fat-scale/body-data/auto-link',
						fail(err) {
							console.log(err);
						}
					})
				} else {
					getApp().globalData.selmac = {
						deviceId: this.healthData.deviceld
					}
					uni.navigateTo({
						url: '/health/fat-scale/body-data/auto-link'
					})
				}
			},
			gethealthData() {
				const that = this
				// that.$ajax({
				// 	url: `/api/scale/top/information`,
				// 	type: 'GET',
				// 	successFun: function (res) {
				// 		console.log(res, '获取健康数据')
				// 		that.healthData = res.data
				// 	},
				// 	errorFun: function (error) {
				// 		uni.tools.alert.error(error.error_msg)
				// 	},
				// 	completeFun: function () {
				// 		that.isLoading = true
				// 		that.isLoadingGoods = false
				// 		that.$store.commit('changeBodyData', false)
				// 	}
				// })
			},
			setPadding() {
				const { pt, pr, pb, pl } = this.curStyle.padding || {};
				this.padStyle = `${transformPixel(pt)} ${transformPixel(
					pr
				)} ${transformPixel(pb)} ${transformPixel(pl)}`
			},
			setHealthStyle() {
				const { tr, tl, br, bl } = this.curStyle.contentRadius
				const {pt, pb, pl, pr} = this.curStyle.contentPadding
				const { bgImg, url, scale} = this.curOptions
				this.healthStyle = `
					padding: ${transformPixel(pt)} ${transformPixel(pb)} ${transformPixel(pr)} ${transformPixel(pl)};
					border-radius: ${transformPixel(tl)} ${transformPixel(tr)} ${transformPixel(br)} ${transformPixel(bl)};
					background-image: url(${bgImg ? bgImg.url : this.renovationData.scale});
					background-color: ${!bgImg || !bgImg.url? colorToRgba(this.curStyle.contentColor,): 'transparent'}
				`
			}
		}
	}
</script>

<style lang="scss" scoped>
	.com-health-box {
		padding: r(20px);
	}

	.com-health {
		width: 100%;
		background-image: url(bg('images/selmac/health-bg.png'));
		background-size: 100% 100%;
		background-position: center;
		border-radius: r(20px);
		padding: r(20px);
		overflow: hidden;

		.health-userinfo {
			display: flex;
			align-items: center;
			justify-content: space-between;
			margin-bottom: r(15px);

			.avatar {
				border: r(2px) solid #fff;
				border-radius: 100%;
				flex-shrink: 0;
				width: r(80px);
				height: r(80px);
				border-radius: 50%;
				background-size: 100% 100%;
				background-color: #fff;
			}

			.name-day {
				margin: 0 r(18px);
				display: flex;
				flex-direction: column;
				align-items: flex-start;
				justify-content: center;
				flex: 1;
				color: white;
				font-size: r(24px);

				.name {
					@include wordEllipsisNum(2);
				}

				.max {
					font-size: r(32px);
				}
			}

			.base-info-btn {
				flex-shrink: 0;
				width: r(138px);
				height: r(52px);
				background: #ffffff;
				border-radius: r(26px);
				display: flex;
				align-items: center;
				justify-content: center;
				color: #ff1a1a;
				font-size: r(28px);
			}
		}

		.health-info {
			width: 100%;
			background: #fffdf8;
			box-shadow: 0px 4px 10px 0px rgba(212, 212, 212, 0.3);
			border-radius: r(10px);
			padding: r(20px);
			overflow: hidden;

			.unit {
				font-size: r(30px);
			}

			.weight-box {
				position: relative;
				width: calc(100% + 40rpx);
				left: -20rpx;
				display: flex;
				align-items: center;
				justify-content: space-between;
				margin-bottom: r(30px);

				.item {
					flex: 1;
					min-height: r(85px);
					flex-shrink: 0;
					color: #000000;
					font-size: r(40px);
					display: flex;
					align-items: center;
					flex-direction: column;
					justify-content: center;

					.num {
						line-height: r(56px);
						font-weight: 600;
						margin-bottom: r(12px);
					}

					.desc {
						color: #999999;
						font-size: r(24px);
						line-height: r(34px);
					}

					&.current-weight {
						position: relative;
						.icon{
							position: absolute;
							
							right: 0;
							top: 50%;
							transform: translateY(-50%);
							width: r(30px);
							height: r(30px);
							background-image: url(bg(
								'images/selmac/arrow-right-grey.png'
							));
						}

						.num {
							position: relative;
							color: #ff1a1a;
							
						}
						.line{
							position: absolute;
							bottom: r(3px);
							left: 0;
							width: 100%;
							height: r(4px);
						}

						&::before,
						&::after {
							position: absolute;
							content: '';
							width: r(1px);
							height: r(170px);
							top: 50%;
							transform: translateY(-50%) scale(0.5);
							transform-origin: center center;
							background-color: rgba(
								$color: #979797,
								$alpha: 0.1
							);
						}

						&::before {
							left: 0;
						}

						&::after {
							right: 0;
						}
					}
				}
			}

			.card {
				display: flex;
				align-items: center;
				justify-content: space-between;

				.card-item {
					flex-shrink: 0;
					width: r(110px);
					height: r(110px);
					background: #ffe3e3;
					border-radius: r(10px);
					display: flex;
					align-items: center;
					flex-direction: column;
					justify-content: center;
					color: #000000;
					font-size: r(28px);
					line-height: r(40px);
					font-weight: 600;

					.desc {
						color: #666666;
						font-weight: 400;
						font-size: r(24px);
						line-height: r(34px);
					}

					&.long {
						width: r(232px);
						height: r(110px);
						background: #ff1a1a;
						border-radius: r(20px);
						color: white;
						font-size: r(32px);
						padding-right: r(86px);
						background-image: url(bg('images/selmac/cheng.png'));
						background-size: r(28px) r(28px);
						background-position: r(154px) r(40px);
						background-repeat: no-repeat;
						align-items: flex-end;
					}
				}
			}
		}
	}
</style>
