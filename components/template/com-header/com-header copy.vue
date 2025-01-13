<template>
	<view class="com-header">
		<com-navigation
			:backgroundColor="header.style.bgColor || ''"
			:bgImg="header.content.bgImg.url || ''"
			:pageBg="pageBg"
			:showType="header.options.showType"
			alignItems="normal"
		>
			<view class="navigation-slot" :style="{ paddingLeft: pl }">
				<view
					class="default-back"
					v-if="showBack && !header.search.prefixIcon.url"
					@click="backPage"
				></view>
				<view
					class="prefix-icon"
					v-if="header.search.prefixIcon.url"
					:style="iconStyle"
				>
					<image
						class="img"
						:src="header.search.prefixIcon.url"
						mode="heightFix"
						@load="iconLoad"
						@error="iconError"
						@click="jumpLink(header.search.prefixLink)"
						v-if="header.search.isShowPrefixIcon"
					></image>
				</view>
				<view
					class="search-bar"
					:style="searchStyle"
					v-if="header.options.type === 2"
					@click="jumpSearchPage"
				>
					<view class="search-icon">
						<uni-icons
							type="search"
							:size="20"
							:color="header.search.color"
						></uni-icons>
					</view>
					<view
						class="search-inp"
						:style="{
							color: header.search.color,
							justifyContent: justifyContent[header.search.textAlign]
						}"
					>{{header.search.placeholder}}</view>
				</view>
				<view
					class="search-title"
					:style="titleStyle"
					v-else
				>
					{{ header.content.title }}
				</view>
			</view>
		</com-navigation>
	</view>
</template>

<script>
	import {
		colorToRgba,
		transformPixel,
		jumpLink
	} from '@/components/template/utils.js'
	export default {
		name: 'ComHeader',
		props: {
			curData: {
				type: Object,
				default() {
					return {}
				}
			},
			pageBg: {
				type: String,
				default: ''
			},
			showBack: {
				type: Boolean,
				default: false
			}
		},
		data() {
			return {
				searchHieght: '',
				searchStyle: '',
				iconStyle: '',
				pl: '',
				titleStyle: '',
				fontStyle: {},
				systemInfo: {},
				rect: {},
				header: {},
				isLoad: false,
				justifyContent: {
					left: 'flex-start',
					center: 'center',
					right: 'flex-end'
				}
			}
		},
		created() {
			this.systemInfo = getApp().globalData.systemInfo
			this.rect = getApp().globalData.rect
			this.init()
		},
		computed: {
			isShowSearch() {
				if (!this.header.search.isShowPrefixIcon) {
					return ''
				} else if (this.header.search.isShowPrefixIcon && this.isLoad) {
					return ''
				} else {
					return 'display:none;'
				}
			}
		},
		methods: {
			jumpLink,
			// 跳转搜索页
			jumpSearchPage() {
				uni.tools.url.toUrl('/shop/search', {search_word: this.header.search.placeholder})
			},
			backPage() {
				const pagesLen = getCurrentPages().length
				if(pagesLen > 1) {
					uni.navigateBack({
						delta: 1
					})
				}else{
					uni.reLaunch({
						url: '/pages/index/index'
					})
				}
			},
			init() {
				this.header = {
					key: this.curData.key,
					...this.curData.data
				}
				this.setSearchStyle()
				this.setIconStyle()
				this.setTitleStyle()
			},
			setSearchStyle() {
				const { borderRadius, bgColor, opacity, color, textAlign } =
					this.header.search
				const { fontSize } = this.header.style
				const rect = this.rect
				const styleArr = []
				styleArr.push(
					`height:${rect.height}px`
				)
				styleArr.push(`border-radius: ${transformPixel(borderRadius)}`)
				styleArr.push(
					`background-color: ${colorToRgba(bgColor, opacity)}`
				)
				styleArr.push(`font-size: ${transformPixel(fontSize)}`)
				styleArr.push(`color: ${color}`)
				styleArr.push(`text-align: ${textAlign}`)
				this.searchStyle = styleArr.join(';') + ';'
				this.searchHieght = rect.height
			},
			setIconStyle() {
				const { iconPl, iconPr, pl } = this.header.style
				const rect = this.rect
				const styleArr = []
				this.pl = transformPixel(pl)
				this.iconStyle = `margin-right: ${transformPixel(
					iconPr
				)};height:${
					rect.height
				}px;`
			},
			setTitleStyle() {
				const { fontSize, fontWeight, color } = this.header.style
				const styleArr = []
				styleArr.push(`font-weight: ${fontWeight}`)
				styleArr.push(`font-size: ${transformPixel(fontSize)}`)
				styleArr.push(`color: ${color}`)
				// #ifndef APP
				styleArr.push(
					`padding: 0 ${
						this.systemInfo.windowWidth - this.rect.left + 10
					}px;`
				)
				// #endif
				this.titleStyle = styleArr.join(';')
			},
			iconLoad() {
				this.isLoad = true
			},
			iconError() {
				this.isLoad = true
			}
		}
	}
</script>

<style lang="scss">
	.com-header {
		.navigation-slot {
			/* #ifdef H5 */
			margin-top: r(20px);
			/* #endif */
			display: flex;
			height: 100%;
			align-items: center;
			padding-right: r(20px);
			overflow: hidden;
			position: relative;
			.default-back {
				position: absolute;
				top: 50%;
				width: 50rpx;
				height: 50rpx;
				background: url(bg('images/selmac/arrow-left-black.png')) no-repeat 0 center/r(36px) r(36px);
				transform: translateY(-50%);
				z-index: 2;
			}
			.prefix-icon {
				flex-shrink: 0;
				.img {
					width: auto;
					height: 100%;
				}
			}

			.search-bar {
				position: relative;
				flex: 1;
				height: 32px;
				// margin-top: 4px;
				overflow: hidden;
				padding: 0 r(30px);
				padding-left: r(60px);
				display: flex;

				.search-icon {
					position: absolute;
					width: r(40px);
					height: r(40px);
					left: r(14px);
					top: 50%;
					transform: translateY(-50%);
				}

				.search-inp {
					width: 100%;
					height: 100%;
					display: flex;
					align-items: center;
					pointer-events: none;
				}
			}

			.search-title {
				position: absolute;
				top: 50%;
				left: 0;
				width: 750rpx;
				transform: translateY(-50%);
				flex: 1;
				font-weight: normal;
				text-align: center;
				@include wordEllipsisNum(2);
			}
		}
	}
</style>
