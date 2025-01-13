<template>
	<view class="com-blank" :style="blankStyle">
		<text
			:style="{
				fontSize: transformPixel(curData.style.fontSize),
				fontWeight: curData.style.fontWeight,
				color: curData.style.color
			}"
			v-if="curData.content.text"
		>
			{{ curData.content.text }}
		</text>
	</view>
</template>

<script>
	import { transformPixel } from '@/components/template/utils.js'
	export default {
		name: 'ComBlank',
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
				blankStyle: ''
			}
		},
		created() {
			this.setStyle()
		},
		methods: {
			transformPixel,
			setStyle() {
				const { bgColor, height, padding } = this.curData.style
				const { align, justify } = this.curData.options
				const { pt, pr, pb, pl } = padding
				this.blankStyle = `background-color:${bgColor};align-items:${align};justify-content:${justify};padding:${transformPixel(
					pt
				)} ${transformPixel(pr)} ${transformPixel(pb)} ${transformPixel(pl)};`
			}
		}
	}
</script>

<style lang="scss">
	.com-blank {
		width: 100%;
		display: flex;
	}
</style>
