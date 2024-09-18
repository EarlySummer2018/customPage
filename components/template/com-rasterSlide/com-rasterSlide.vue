<template>
	<view class="com-rasterSlide" v-if="curData.content.length">
		<com-component-style :curStyle="curData.style || null">
			<com-container-style :curStyle="curData.style || null">
				<view
					class="com-rasterSlide_header"
					v-if="curData.options.mainHeading"
					:style="{
						backgroundImage: `url(${curData.options.headerImg.url})`
					}"
				>
					<view class="com-rasterSlide_title">
						<view
							class="com-rasterSlide_title-main"
							:style="{
								color: curData.style.mainColor
							}"
						>
							{{ curData.options.mainHeading }}
						</view>
						<view
							class="com-rasterSlide_title-sub"
							:style="{
								color: curData.style.subColor
							}"
						>
							{{ curData.options.subHeading }}
						</view>
					</view>
					<view class="com-rasterSlide_btn" v-show="isShow('more')">
						查看更多
					</view>
				</view>
				<view
					class="com-rasterSlide_goods-container"
					:style="{ height: containerHeight }"
				>
					<scroll-view
						scroll-x="true"
						class="com-rasterSlide_goods-scroll-view"
						:style="{ height: scrollViewheight }"
						:bounces="false"
						enhanced
					>
						<view
							class="com-rasterSlide_body"
							:style="{
								justifyContent:
									curData.content.length === 3
										? 'space-between'
										: 'flex-start'
							}"
						>
							<view
								class="com-rasterSlide_body-item"
								v-for="item in curData.content"
								:key="item.id"
								:style="{
									width: itemWidth
								}"
								@click="handleItem(item)"
							>
								<view
									class="com-rasterSlide_body-item-img"
									:style="coverStyle"
								>
									<com-image
										:src="item.main_image || ''"
										:tr="curData.style.imgTR"
										:br="curData.style.imgBR"
										mode="aspectFill"
									/>
								</view>
								<view
									class="com-rasterSlide_body-item-goodsName"
									v-show="isShow('name')"
								>
									<view class="txt">
										{{ item.name }}
									</view>
								</view>
								<view
									class="com-rasterSlide_body-item-price"
									v-show="isShow('cost_price')"
								>
									￥{{ twoDecimals(item.cost_price) }}
								</view>
								<view
									class="com-rasterSlide_body-item-market_price"
									v-show="isShow('market_price')"
								>
									￥{{ twoDecimals(item.market_price) }}
								</view>
							</view>
						</view>
					</scroll-view>
				</view>
			</com-container-style>
		</com-component-style>
	</view>
</template>

<script>
	import { transformPixel, twoDecimals, jumpLink } from '../utils.js'
	export default {
		name: 'ComRasterSlide',
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
				coverStyle: '',
				itemWidth: '',
				containerHeight: '',
				scrollViewheight: ''
			}
		},
		created() {
			this.setGoodsCoverSize()
		},
		methods: {
			twoDecimals,
			handleItem(item) {
				const link = {
					type: 1,
					path: `/shop/goods?id=${item.id}`
				}
				jumpLink(link)
			},
			isShow(field) {
				return this.curData.options.showFields.includes(field)
			},
			setGoodsCoverSize() {
				const len = this.curData.content.length
				if (len <= 3) {
					this.itemWidth = transformPixel(104)
					this.containerHeight = transformPixel(194)
					this.scrollViewheight = transformPixel(214)
					this.coverStyle = `width:${transformPixel(
						104
					)};height:${transformPixel(104)};`
				} else {
					this.itemWidth = transformPixel(84)
					this.coverStyle = `width:${transformPixel(
						84
					)};height:${transformPixel(84)};`
					this.containerHeight = transformPixel(174)
					this.scrollViewheight = transformPixel(194)
				}
			}
		}
	}
</script>

<style lang="scss">
	.com-rasterSlide {
		&_title {
			&-main {
				font-size: r(28px);
				color: #000;
				font-weight: 700;
			}
			&-sub {
				margin-top: r(4px);
				font-size: r(24px);
				color: #313131;
			}
		}
		&_btn {
			padding: 0 r(20px);
			height: r(46px);
			background: #fff;
			font-size: r(24px);
			border-radius: r(200px);
			display: inline-flex;
			align-items: center;
			justify-content: center;
			color: #313131;
		}
		&_header {
			width: 100%;
			height: r(120px);
			padding: 0 r(20px);
			background-size: cover;
			background-repeat: no-repeat;
			display: flex;
			align-items: center;
			justify-content: space-between;
		}
		&_goods-container {
			overflow: hidden;
		}
		&_goods-scroll-view {
			white-space: nowrap;
		}
		&_body {
			display: flex;
			width: fit-content;
			padding: r(20px);
			&-item {
				width: r(208px);
				margin-left: r(20px);
				&:first-child {
					margin-left: 0;
				}
				&-img {
					width: r(208px);
					height: r(208px);
					font-size: r(60px);
				}
				&-goodsName {
					font-size: r(26px);
					height: r(70px);
					color: #475965;
					.txt {
						white-space: pre-wrap;
						@include wordEllipsisNum(2);
					}
				}
				&-price {
					color: #d70f1e;
					font-weight: 600;
					font-size: r(24px);
				}
				&-market_price {
					font-size: r(20px);
					color: #9699a4;
					font-weight: 500;
					text-decoration: line-through;
					/* #ifdef H5 */
					margin-left: r(-28px);
					transform: scale(0.75);
					/* #endif */
				}
			}
		}
	}
</style>
