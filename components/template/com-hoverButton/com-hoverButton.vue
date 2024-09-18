<template>
	<view
		class="com-hoverButton"
		:style="warpStyle + isShow"
		@click.stop="handleJump(curData.options)"
	>
		<button
			class="com-hoverButton_contact"
			open-type="contact"
			type="default"
			v-if="curData.options.type === 1"
		></button>
		<com-image
			:src="curData.options.icon.url || ''"
			:tr="curData.style.imgTR"
			:br="curData.style.imgBR"
		></com-image>
	</view>
</template>

<script>
	import { transformPixel, jumpLink } from '../utils'
	export default {
		name: 'ComHoverButton',
		props: {
			curData: {
				type: Object,
				default() {
					return {}
				}
			},
			curScrollTop: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				warpStyle: ''
			}
		},
		created() {
			this.setContianerStyle()
		},
		computed: {
			isShow() {
				const { type, top } = this.curData.options
				if (type !== 4 || !top)
					return `opacity: 1;transition: opacity 0.2s linear;`
				else {
					if (
						transformPixel(Number(top), 'number') >
						this.curScrollTop
					) {
						return `opacity: 0;transition: opacity 0.2s linear;`
					} else {
						return `opacity: 1;transition: opacity 0.2s linear;`
					}
				}
			}
		},
		methods: {
			handleJump(options) {
				if (options.type === 2) {
					uni.makePhoneCall({
						phoneNumber: String(options.phone)
					})
				} else if (options.type === 3) {
					jumpLink(options.link)
				} else if (options.type === 4) {
					this.$emit('scrollTop', options.top)
				}
			},
			setContianerStyle() {
				const { bottom, right, opacity } = this.curData.style
				const systemInfo = getApp().globalData.systemInfo
				const safeArea =
					systemInfo.screenHeight - systemInfo.safeArea.bottom
				const arr = []
				arr.push(`right: ${transformPixel(right)};`)
				arr.push(`opacity: ${opacity};`)
				arr.push(`bottom: ${transformPixel(bottom + 55 + safeArea)};`)
				this.warpStyle = arr.join('')
			}
		}
	}
</script>

<style lang="scss">
	.com-hoverButton {
		position: fixed;
		z-index: 99;
		right: 0;
		width: r(110px);
		height: r(110px);
		bottom: r(110px);
		opacity: 0;
		// cursor: pointer;
		// box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
		&_contact {
			position: absolute;
			width: 100%;
			height: 100%;
			opacity: 0;
			z-index: 3;
		}
	}
</style>
