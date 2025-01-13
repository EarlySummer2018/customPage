<template>
	<view class="com-component-style" :style="conStyle">
		<slot></slot>
	</view>
</template>

<script>
	import { colorToRgba, transformPixel } from '../utils.js'
	export default {
		name: 'ComComponentStyle',
		props: {
			curStyle: {
				type: Object,
				default() {
					return {}
				}
			}
		},
		data() {
			return {
				conStyle: {}
			}
		},
		created() {
			this.setContainerStyle()
		},
		methods: {
			setContainerStyle() {
				const { padding, bgColor, opacity } = this.curStyle
				const { pt, pr, pb, pl } = padding
				this.conStyle = `padding: ${transformPixel(
					pt
				)} ${transformPixel(pr)} ${transformPixel(pb)} ${transformPixel(pl)};background-color:${colorToRgba(
					bgColor,
					opacity
				)};`
			}
		}
	}
</script>
<style lang="scss" scoped>
	.com-component-style {
		overflow: hidden;
	}
</style>
