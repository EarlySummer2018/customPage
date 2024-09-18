<template>
	<view class="com-multiImage">
		<com-component-style :curStyle="curData.style || null">
			<com-container-style :curStyle="curData.style || null">
				<view class="com-multiImage_content" :style="contentStyle">
					<view class="com-multiImage_header" :style="headerStyle" v-if="curData.options.mainHeading">
						<view class="com-multiImage_header-main" :style="{
								color: curData.style.mainColor,
								fontSize: transformPixel(curData.style.mfs)
							}">
							<text class="com-multiImage_header-title">
								{{ curData.options.mainHeading }}
							</text>
						</view>
						<view class="com-multiImage_header-sub" :style="{
								color: curData.style.subColor,
								fontSize: transformPixel(curData.style.sfs)
							}">
							<text class="com-multiImage_header-title">
								{{ curData.options.subHeading }}
							</text>
						</view>
					</view>
					<view class="com-multiImage_warp" :style="warpStyle" v-if="curData.options.row !== -1">
						<view class="com-multiImage_warp-img" :style="imgStyle" v-for="item in curData.content"
							:key="item.key" @click="jumpLink(item.link)">
							<com-image curStyle="vertical-align:bottom;" :src="item.img.url || ''" :minHeight="130"
								:tr="curData.style.imgTR" :br="curData.style.imgBR" mode="widthFix"></com-image>
						</view>
					</view>
					<view class="com-multiImage_window" v-else>
						<view class="com-multiImage_window-left" v-if="curData.content[0]" @click="jumpLink(curData.content[0].link)">
							<com-image curStyle="vertical-align:bottom;" :src="curData.content[0].img.url || ''"
								:tr="curData.style.imgTR" :br="curData.style.imgBR" :minHeight="130"
								mode="aspectFill"></com-image>
						</view>
						<view class="com-multiImage_window-right">
							<view class="com-multiImage_window-right-top" v-if="curData.content[1]" @click="jumpLink(curData.content[1].link)">
								<com-image curStyle="vertical-align:bottom;" :src="curData.content[1].img.url || ''"
									:tr="curData.style.imgTR" :br="curData.style.imgBR" :minHeight="130"
									mode="aspectFill"></com-image>
							</view>
							<view class="com-multiImage_window-right-bottom">
								<view class="com-multiImage_window-right-bottom-left" v-if="curData.content[2]" @click="jumpLink(curData.content[2].link)">
									<com-image curStyle="vertical-align:bottom;" :src="curData.content[2].img.url || ''"
										:tr="curData.style.imgTR" :br="curData.style.imgBR" :minHeight="130"
										mode="aspectFill"></com-image>
								</view>
								<view class="com-multiImage_window-right-bottom-right" v-if="curData.content[3]" @click="jumpLink(curData.content[3].link)">
									<com-image curStyle="vertical-align:bottom;" :src="curData.content[3].img.url || ''"
										:tr="curData.style.imgTR" :br="curData.style.imgBR" :minHeight="130"
										mode="aspectFill"></com-image>
								</view>
							</view>
						</view>
					</view>
				</view>
			</com-container-style>
		</com-component-style>
	</view>
</template>

<script>
	import {
		transformPixel,
		jumpLink
	} from '../utils'
	export default {
		name: 'ComMultiImage',
		props: {
			curData: {
				type: Object,
				default () {
					return {}
				}
			}
		},
		data() {
			return {
				warpStyle: '',
				imgStyle: '',
				headerStyle: '',
				contentStyle: ''
			}
		},
		created() {
			this.setWarpStyle()
			this.setImgStyle()
			this.setContentStyle()
		},
		methods: {
			jumpLink,
			transformPixel,
			setWarpStyle() {
				const {
					row
				} = this.curData.options
				if (row !== -1) {
					this.warpStyle = `grid-template-columns: repeat(${row}, 1fr);`
				}
			},
			setImgStyle() {
				const {
					imgPy,
					imgPx
				} = this.curData.style
				this.imgStyle = `padding: ${transformPixel(
					imgPy
				)} ${transformPixel(imgPx)};`
			},
			setContentStyle() {
				const {
					contentPx,
					contentPy
				} = this.curData.style
				const {
					mainHeading
				} = this.curData.options
				this.headerStyle = `top: -${transformPixel(
					contentPy
				)};left: -${transformPixel(
					contentPx
				)};width: calc(100% + ${transformPixel(contentPx * 2)});`
				const pt = Math.abs(40 - contentPy)
				this.contentStyle = `padding-top:${
					mainHeading ? transformPixel(pt) : '0px'
				};`
			}
		}
	}
</script>

<style lang="scss">
	.com-multiImage {
		&_content {
			position: relative;
		}

		&_header {
			position: absolute;
			padding: r(20px);
			display: flex;
			align-items: center;
			top: 0;
			left: 0;
			width: 100%;
			height: r(80px);

			&-title {
				@include wordEllipsisNum(1);
			}

			&-main {
				display: inline-block;
				font-size: 28rpx;
				color: #475965;
				font-weight: 700;
			}

			&-sub {
				position: relative;
				display: inline-block;
				// height: 30rpx;
				font-size: 22rpx;
				color: #475965;
				font-size: 400;
				padding-left: 15rpx;
				margin-left: 15rpx;

				&::before {
					position: absolute;
					content: '';
					top: 50%;
					left: -0.5px;
					width: 1rpx;
					height: 48rpx;
					background-color: #475965;
					transform: translateY(-50%) scale(0.5);
				}
			}
		}

		&_warp {
			display: grid;
			align-items: center;
		}

		&_window {
			display: flex;
			align-items: center;
			height: 375rpx;

			&-left,
			&-right {
				width: 50%;
				flex-shrink: 0;
				height: 100%;
			}

			&-right {
				display: flex;
				flex-direction: column;

				&-top,
				&-bottom {
					width: 100%;
					height: 50%;
					flex-shrink: 0;
				}

				&-bottom {
					display: flex;
					align-items: center;

					&-left,
					&-right {
						flex: 1;
						flex-shrink: 0;
						height: 100%;
					}
				}
			}
		}
	}
</style>