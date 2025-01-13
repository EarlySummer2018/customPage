<template>
	<view class="com-swiper">
		<com-component-style :curStyle="curData.style || null">
			<view class="com-swiper_warp">
				<swiper
					:style="swiperStyle"
					:indicator-dots="false"
					:autoplay="curData.options.autoPlay"
					:interval="curData.options.interval * 1000"
					:circular="curData.options.loop"
					@change="changeCurrent"
				>
					<swiper-item
						v-for="item in curData.content"
						:key="item.key"
						@click="hadleItem(item)"
					>
						<view
							class="com-swiper_item"
							:style="{
								borderRadius:
									curData.content.length === 1 && borderRadius
							}">
							<com-image
								:src="item.img.url || ''"
								:imgRadius="imgRadius"
								mode="aspectFill"
							></com-image>
						</view>
					</swiper-item>
				</swiper>
				<view class="com-swiper_dot" :style="dotContainerStyle">
					<view
						class="com-swiper_dot-item"
						v-for="item in curData.content.length"
						:key="item"
						:style="
							dotStyle +
							(current === item
								? `background-color: ${curData.style.dotActiveColor}`
								: '')
						"></view>
				</view>
			</view>
		</com-component-style>
	</view>
</template>

<script>
	import {
		colorToRgba,
		transformPixel,
		jumpLink
	} from '@/components/template/utils.js'
	export default {
		name: 'ComSwiper',
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
				current: 0,
				swiperStyle: '',
				dotStyle: '',
				activeBgColor: '',
				dotContainerStyle: '',
				borderRadius: '0px',
				imgRadius: ''
			}
		},
		created() {
			this.setSwiperStyle()
			this.setDotStyle()
			this.setDotContainerStyle()
		},
		methods: {
			jumpLink,
			hadleItem(item) {
				jumpLink(item.link)
			},
			changeCurrent(e) {
				this.current = e.detail.current
			},
			setSwiperStyle() {
				const {
					borderRadius,
					imgRadius,
					height
				} = this.curData.style
				const styleArr = []
				styleArr.push(`border-radius: ${transformPixel(borderRadius)};`)
				styleArr.push(`height: ${transformPixel(height)};`)
				styleArr.push('overflow: hidden;')
				this.borderRadius = transformPixel(borderRadius)
				this.swiperStyle = styleArr.join('')
				const { tr, tl, br, bl } = imgRadius || {};
				this.imgRadius = `border-radius:${transformPixel(tl)} ${transformPixel(tr)} ${transformPixel(br)} ${transformPixel(bl)};`
			},
			setDotStyle() {
				const {
					dotMx,
					dotWidth,
					dotBorderRadius,
					dotColor
				} =
				this.curData.style
				const {
					dotDirection
				} = this.curData.options
				const styleArr = []
				styleArr.push(`margin: 0 ${transformPixel(dotMx)};`)
				styleArr.push(`width: ${transformPixel(dotWidth)};`)
				styleArr.push(
					`border-radius: ${transformPixel(dotBorderRadius)};`
				)
				styleArr.push(`background-color: ${dotColor};`)
				this.dotStyle = styleArr.join('')
			},
			setDotContainerStyle() {
				const {
					dotContainerPx
				} = this.curData.style
				const {
					dotDirection
				} = this.curData.options
				this.dotContainerStyle = `padding: 0 ${transformPixel(
					dotContainerPx
				)};justify-content: ${dotDirection};`
			}
		}
	}
</script>

<style lang="scss">
	.com-swiper {
		position: relative;
		overflow: hidden;

		&_warp {
			position: relative;
		}

		&_item {
			width: 100%;
			height: 100%;
			overflow: hidden;
		}

		&_dot {
			position: absolute;
			width: 100%;
			bottom: 20rpx;
			display: flex;
			align-items: center;
			justify-content: center;

			&-item {
				width: 10rpx;
				height: 10rpx;
				border-radius: 50%;
			}
		}
	}
</style>