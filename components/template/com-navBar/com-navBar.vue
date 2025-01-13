<template>
	<view class="com-navBar">
		<com-component-style :curStyle="curData.style || null">
			<com-container-style :curStyle="curData.style || null">
				<view class="com-navBar_list" :style="layoutStyle">
					<view
						class="com-navBar_list-item"
						v-for="item in curData.content"
						:key="item.key"
						@click="handleItem(item)"
					>
						<view class="com-navBar_list-item-cover" :style="imgStyle">
							<com-image
								:src="item.img.url || ''"
							></com-image>
						</view>
						<view
							:style="{
								color: curData.style.color
							}"
						>
							{{ item.title }}
						</view>
					</view>
				</view>
			</com-container-style>
		</com-component-style>
	</view>
</template>

<script>
	import { mapState } from 'vuex'
	import { jumpLink, transformPixel } from '../utils'
	export default {
		name: 'ComNavBar',
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
				layoutStyle: '',
				imgStyle: ''
			}
		},
		created() {
			this.setLayoutStyle()
		},
		computed: {
			...mapState({
				status: (state) => state.status
			})
		},
		methods: {
			setLayoutStyle() {
				this.layoutStyle = `grid-template-columns: repeat(${this.curData.options.row}, 1fr)`
				const { tr, tl, br, bl } = this.curData.style.imgRadius
				this.imgStyle = `border-radius:${transformPixel(tl)} ${transformPixel(tr)} ${transformPixel(br)} ${transformPixel(bl)};`
			},
			handleItem(item) {
				jumpLink(item.link)
			}
		}
	}
</script>

<style lang="scss">
	.com-navBar {
		&_list {
			display: grid;
			gap: r(20px) 0;
			&-item {
				flex: 1;
				display: inline-flex;
				align-items: center;
				flex-direction: column;
				justify-content: center;
				&-cover {
					width: r(90px);
					height: r(90px);
					margin-bottom: r(10px);
					overflow: hidden;
				}
			}
		}
	}
</style>
