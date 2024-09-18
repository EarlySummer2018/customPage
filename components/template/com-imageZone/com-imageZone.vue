<template>
	<view class="com-image-zone">
		<com-component-style :curStyle="curData.style || null">
			<com-image
				:src="curData.options.bgImg.url || ''"
				:tr="curData.style.imgTR"
				:br="curData.style.imgBR"
				mode="widthFix"
			></com-image>
			<view
				class="com-image-zone_item"
				v-for="(item, index) in curData.content"
				:style="{
					transform: `translate3d(${transformPixel(
						item.x
					)}, ${transformPixel(item.y)}, 0px)`,
					width: transformPixel(item.width),
					height: transformPixel(item.height),
					zIndex: index + 3,
					backgroundImage: item.bgImg.url ? `url(${item.bgImg.url})` : ''
				}"
				@click="handleZoneItem(item)"
			>
				<view class="com-image-zone_item-content">
					<view class="com-image-zone_item-title" v-show="item.showTitle">
						<text class="txt">{{ item.title }}</text>
					</view>
					<view v-show="item.showDot" class="click-dot"></view>
				</view>
			</view>
		</com-component-style>
	</view>
</template>

<script>
	import { transformPixel, jumpLink } from '../utils'
	export default {
		props: 'ComImageZone',
		props: {
			curData: {
				type: Object,
				default() {
					return {}
				}
			}
		},
		methods: {
			transformPixel,
			handleZoneItem(item) {
				jumpLink(item.link)
			}
		}
	}
</script>

<style lang="scss">
	.com-image-zone {
		position: relative;
		&_item {
			position: absolute;
			top: 0;
			left: 0;
			background-position: 50% center;
			    background-size: 100%;
			    background-repeat: no-repeat;
			&-content {
				position: absolute;
				width: 100%;
				height: 100%;
				top: 0;
				left: 0;
				display: flex;
				align-items: center;
				flex-direction: column;
				justify-content: center;
				z-index: 2;
				pointer-events: none;
			}
			&-title {
				position: relative;
				font-size: r(16px);
				color: white;
				background-color: rgba($color: #000000, $alpha: 0.8);
				padding: r(6px) r(20px);
				border-radius: r(40px);
				margin-bottom: r(10px);
				display: inline-block;
				&::after {
					position: absolute;
					content: '';
					width: 0;
					height: 0;
					bottom: r(-14px);
					left: 50%;
					border: r(8px) solid transparent;
					border-top-color: #000000;
					transform: translateX(-50%);
				}
			}
			.click-dot {
				position: relative;
				width: r(20px);
				height: r(20px);
				&::before {
					position: absolute;
					content: '';
					top: 0;
					left: 0;
					width: 100%;
					height: 100%;
					background-color: rgba($color: #000000, $alpha: 1);
					border-radius: 50%;
				}
				&::after {
					position: absolute;
					content: '';
					top: 50%;
					left: 50%;
					width: 50%;
					height: 50%;
					transform: translate(-50%, -50%);
					background-color: #fff;
					border-radius: 50%;
				}
			}
		}
	}
</style>
