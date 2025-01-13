<template>
	<view class="com-contianer-style" :style="conStyle">
		<slot></slot>
	</view>
</template>

<script>
	import { colorToRgba, transformPixel } from '../utils.js'
	export default {
		name: 'ComContianerStyle',
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
				const { contentColor, contentOpacity, contentRadius, contentPadding } = this.curStyle;
				const { tl, tr, br, bl } = contentRadius;
				const { pt, pr, pb, pl } = contentPadding || {};
				this.conStyle = `
					border-radius: ${transformPixel(tl)} ${transformPixel(tr)} ${transformPixel(br)} ${transformPixel(bl)};
					background-color:${colorToRgba(contentColor, contentOpacity )};
					padding: ${transformPixel(pt)} ${transformPixel(pr)} ${transformPixel(pb)} ${transformPixel(pl)};
					overflow:hidden;`
			}
		}
	}
</script>
