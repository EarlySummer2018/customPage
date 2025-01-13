<template>
	<view class="com-search">
		<com-component-style :curStyle="curData.style || null">
			<view class="com-search_warp">
				<view class="com-search_warp-prefix-icon" v-if="curData.options.isShowPrefixIcon">
					<com-image :src="curData.content.prefixIcon.url || ''" mode="heightFix" @click="jumpLink(curData.content.prefixLink)"
						bgColor="transparent"></com-image>
				</view>
				<view class="com-search_warp-search-bar" :style="searchStyle" @click="jumpSearchPage">
					<view class="com-search_warp-search-icon">
						<uni-icons type="search" :size="20" :color="curData.style.color"></uni-icons>
					</view>
					<input type="text" class="com-search_warp-search-inp" :placeholder="curData.content.placeholder" />
				</view>
				<view class="com-search_warp-suffix-icon" v-if="curData.options.isShowSuffixIcon">
					<com-image :src="curData.content.suffixIcon.url || ''" mode="heightFix" bgColor="transparent"
						@click="jumpLink(curData.content.suffixLink)"></com-image>
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
		name: 'ComSearch',
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
				searchStyle: ''
			}
		},
		created() {
			this.setSearchStyle()
		},
		methods: {
			jumpSearchPage() {
				uni.tools.url.toUrl('/shop/search')
			},
			jumpLink,
			setSearchStyle() {
				const {
					borderRadius,
					contentColor,
					contentOpacity,
					color,
					textAlign
				} = this.curData.style
				const styleArr = []
				styleArr.push(`border-radius: ${transformPixel(borderRadius)}`)
				styleArr.push(
					`background-color: ${colorToRgba(
						contentColor,
						contentOpacity
					)}`
				)
				styleArr.push(`color: ${color}`)
				styleArr.push(`text-align: ${textAlign}`)
				this.searchStyle = styleArr.join(';') + ';'
			}
		}
	}
</script>

<style lang="scss">
	.com-search {
		&_warp {
			display: flex;
			align-items: center;
			justify-content: center;

			&-suffix-icon,
			&-prefix-icon {
				width: auto;
				height: r(60px);
			}

			&-prefix-icon {
				margin-right: r(20px);
			}

			&-suffix-icon {
				margin-left: r(20px);
			}

			&-search-bar {
				position: relative;
				display: flex;
				height: r(60px);
				flex: 1;
				overflow: hidden;
				padding-left: r(60px);
				padding-right: r(30px);
			}

			&-search-icon {
				position: absolute;
				width: r(40px);
				height: r(40px);
				left: r(14px);
				top: r(12px);
			}

			&-search-inp {
				width: 100%;
				height: 100%;
			}
		}
	}
</style>