<template>
	<view class="com-messageSwiper">
		<com-component-style :curStyle="curData.style || null">
			<view class="com-messageSwiper_warp" :style="warpStyle">
				<view
					class="com-messageSwiper_icon"
					v-if="curData.options.position === 'left'"
					@click="jumpLink(curData.content.link)"
					style="margin-right: 10rpx"
				>
					<com-image
						:src="curData.content.img.url || ''"
						mode="heightFix"
						curStyle="height: 100%"
						:tr="curData.style.imgTR"
						:br="curData.style.imgBR"
					></com-image>
				</view>
				<swiper
					class="com-messageSwiper_swiper"
					circular
					:indicator-dots="false"
					:autoplay="curData.options.autoPlay"
					:interval="curData.options.interval * 1000"
					:vertical="curData.options.direction === 'vertical'"
				>
					<swiper-item
						class="com-messageSwiper_swiper-item"
						v-for="item in list"
						:key="item.id"
						@click="handleItem(item)"
					>
						<view
							class="com-messageSwiper_swiper-item-txt"
							:style="{
								color: curData.style.color
							}"
						>
							{{ item.title }}
						</view>
					</swiper-item>
				</swiper>
				<view
					class="com-messageSwiper_icon"
					v-if="curData.options.position === 'right'"
					@click="jumpLink(curData.content.link)"
					style="margin-left: 10rpx"
				>
					<com-image
						:src="curData.content.img.url || ''"
						mode="heightFix"
						curStyle="height: 100%"
						:tr="curData.style.imgTR"
						:br="curData.style.imgBR"
					></com-image>
				</view>
			</view>
		</com-component-style>
	</view>
</template>

<script>
	import { colorToRgba, transformPixel, jumpLink } from '../utils'
	export default {
		name: 'ComMessageSwiper',
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
				warpStyle: '',
				list: []
			}
		},
		created() {
			this.setWarpStyle()
			this.getList()
		},
		methods: {
			jumpLink,
			handleItem(item) {
				if (this.curData.source === 1) return
				uni.navigateTo({
					url: `/shop/msgdetails?id=${item.id}`
				})
			},
			setWarpStyle() {
				const { borderRadius, contentOpacity, contentColor } =
					this.curData.style
				const arr = []
				arr.push(`border-radius: ${transformPixel(borderRadius)};`)
				arr.push(
					`background-color: ${colorToRgba(
						contentColor,
						contentOpacity
					)};`
				)
				this.warpStyle = arr.join('')
			},
			getList(page) {
				let that = this,
					ajaxData = {
						page: 1
					}
				let Url = ''
				if (that.curData.options.source === 2) {
					Url = '/api/shop/log/messages'
				} else if (that.curData.options.source === 3) {
					Url = '/api/system/notices'
				}
				if (!Url) {
					that.list.push({ title: '暂无消息' })
					return
				}
				that.$ajax({
					url: Url,
					ajaxData: ajaxData,
					type: 'GET',
					successFun(res) {
						that.list = res.list.data
						if (!res.list.data.length) {
							that.list = [{ title: '暂无消息' }]
						}
					},
					errorFun: function (error) {
						uni.tools.alert.error(error.error_msg)
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.com-messageSwiper {
		&_warp {
			padding: r(10px);
			display: flex;
			align-items: center;
			justify-content: flex-start;
			overflow: hidden;
		}
		&_icon {
			height: r(50px);
		}
		&_swiper {
			flex: 1;
			height: r(50px);
			&-item {
				display: flex;
				align-items: center;
				justify-content: flex-start;
				&-txt {
					@include wordEllipsisNum(1);
				}
			}
		}
	}
</style>
