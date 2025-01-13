<template>
	<com-component-style :curStyle="curData.style || null">
		<view class="com-polyline" :style="polylineStyle">
		</view>
	</com-component-style>
</template>

<script>
	import {
		transformPixel
	} from '@/components/template/utils.js'
	export default {
		name: 'ComPolyline',
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
				polylineStyle: ''
			};
		},
		created() {
			this.setStyle()
		},
		methods: {
			setStyle() {
				const {
					lineColor,
					lineSize,
					lineStyle
				} = this.curData.style;
				this.polylineStyle = `border-top: ${transformPixel(lineSize)} ${lineStyle} ${lineColor};`
			}
		}
	}
</script>

<style lang="scss">
	.com-polyline {
		width: 100%;
		height: 0;
		border: none;
	}
</style>
