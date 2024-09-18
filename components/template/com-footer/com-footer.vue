<template>
	<view class="com-footer" :class="{
		'border-top': current === 'cart'
	}" v-if="curData.content.length > 1">
		<view class="com-footer_warp">
			<view class="com-footer_warp-item" v-for="item in curData.content" :key="item.key"
				@click="changeCurrent(item)">
				<view class="com-footer_warp-icon">
					<com-image :src="
							(current === item.link.navIndex
								? item.activeIcon.url
								: item.icon.url) || ''
						" :tr="curData.style.imgTR" :br="curData.style.imgBR" size="15"></com-image>
					<view class="com-footer_warp-cart-num" v-if="cartNum && item.link.navIndex === 'cart'">
						<uni-badge class="uni-badges" :style="{
								'background-color': item.activeColor
							}" :text="cartNum" :type="item.activeColor" :inverted="true" color="white" fontSize="40"
							size="mini"></uni-badge>
					</view>
				</view>
				<view class="com-footer_warp-text" :style="{
						color:
							current === item.link.navIndex
								? item.activeColor
								: item.color
					}">
					{{ item.text }}
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import {
		jumpLink
	} from '@/components/template/utils.js'
	import uniBadge from '@/components/uni-ui/uni-badge.vue'
	import {
		mapState
	} from 'vuex'
	export default {
		name: 'ComFooter',
		components: {
			uniBadge
		},
		props: {
			curData: {
				type: Object,
				default () {
					return {}
				}
			},
			curIndex: {
				type: String,
				default: ''
			},
			footerCurrent: {
				type: String,
				default: ''
			}
		},
		data() {
			return {
				current: ''
			}
		},
		computed: {
			...mapState({
				cartNum: (state) => state.cartNum
			}),
		},
		watch: {
			footerCurrent: {
				handler(newValue,oldValue){
					this.current = newValue
				}
			}
		},
		created() {
			this.current =
				this.curIndex || this.curData.content[0].link.navIndex
		},
		methods: {
			changeCurrent(item) {
				let navTitle = ''
				if (item.link.type !== 99) {
					jumpLink(item.link)
					return
				}
				this.current = item.link.navIndex
				if (item.link.navIndex !== 'index') {
					navTitle = item.link.navTitle || item.text
				}
				this.$emit('changeIndex', {
					navIndex: item.link.navIndex,
					navTitle
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.com-footer {
		position: fixed;
		left: 0;
		bottom: 0;
		width: 100%;
		background-color: #fff;
		box-sizing: content-box;
		z-index: 99;
		box-shadow: 0 0 10px 0 #ccc;
		@include pdenv();

		&.border-top {
			border-top: r(1px) solid #ccc;
			box-shadow: none;
		}

		&_warp {
			height: 110rpx;
			display: flex;
			align-items: center;
			justify-content: center;

			&-item {
				flex: 1;
				display: flex;
				align-items: center;
				flex-direction: column;
				justify-content: center;
			}

			&-icon {
				position: relative;
				width: r(50px);
				height: r(50px);
				z-index: 1;
			}

			&-cart-num {
				position: absolute;
				top: 0;
				right: 0;
				z-index: 2;

				.uni-badges {
					position: absolute;
					top: r(-12px);
					right: r(-20px);
					transform: scale(0.8);
					background-color: $c1;
					border-radius: r(100px);
					color: #fff;
				}
			}

			&-text {
				font-size: r(20px);
				margin-top: r(5px);
			}
		}
	}
</style>