<template>
	<view class="com-multiline" v-if="list.length">
		<com-component-style :curStyle="curData.style || null">
			<com-container-style :curStyle="curData.style || null">
				<view
					class="com-multiline_header"
					v-if="curData.options.mainHeading"
					@click="handleJump"
				>
					<view style="flex: 1">
						<view
							class="com-multiline_header-main-heading"
							:style="{
								color: curData.style.mainColor,
								fontSize: transformPixel(
									curData.style.mfs || 14
								)
							}"
						>
							{{ curData.options.mainHeading }}
						</view>
						<view
							class="com-multiline_header-sub-heading"
							:style="{
								color: curData.style.subColor,
								fontSize: transformPixel(
									curData.style.sfs || 12
								)
							}"
						>
							{{ curData.options.subHeading }}
						</view>
					</view>
					<!-- <view class="com-multiline_header-more"
						v-if="curData.options.showMoreBtn">
						查看更多
					</view> -->
				</view>
				<view
					class="com-multiline_body"
					:class="{
						'pt-20': !curData.options.mainHeading
					}"
				>
					<view
						class="com-multiline_body-item"
						v-for="item in list"
						:key="item.id"
						@click="handleItem(item)"
					>
						<view class="com-multiline_body-item-img">
							<com-image
								:src="item.cover || ''"
								:tr="curData.style.imgTR"
								:br="curData.style.imgBR"
								mode="aspectFill"
							/>
						</view>
						<view class="com-multiline_body-item-info">
							<view class="com-multiline_body-item-title">
								{{ item.title }}
							</view>
							<view class="com-multiline_body-item-desc">
								<text
									v-if="
										isShow('author') && item.author !== ''
									"
								>
									作者 {{ item.author }}
								</text>
								<text v-if="isShow('click_num')">
									浏览 {{ item.click_num }}
								</text>
							</view>
						</view>
					</view>
				</view>
			</com-container-style>
		</com-component-style>
	</view>
</template>

<script>
	import { jumpLink, transformPixel } from '../utils.js'
	export default {
		name: 'ComMultiline',
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
				list: []
			}
		},
		mounted() {
			const { content, other, options } = this.curData
			if (options.source === 1 && other.parent_id && other.child_id) {
				this.getList(other.parent_id, other.child_id, options.row)
			} else {
				this.list = content || []
			}
		},
		methods: {
			jumpLink,
			transformPixel,
			isShow(field) {
				return this.curData.options.showFields.includes(field)
			},
			handleJump() {
				if (
					this.curData.options.showMoreBtn &&
					this.curData.options.source === 1
				)
					return
				const { parent_id, child_id } = this.curData.other
				if (parent_id == '' || child_id == '') return
				const obj = {
					type: 1,
					path: `/categoryList/categoryList?catId=${parent_id}&catSubId=${child_id}`
				}
				jumpLink(obj)
			},
			handleItem(item) {
				let path = `/article/essay/essay?catId=${item.id}`
				if (this.curData.options.source === 1) {
					path = `/article/essay/essay?catId=${item.id}`
				} else {
					path = `/article/essay/essay?id=${item.id}`
				}
				// if (this.curData.options.source === 1) {
				// 	path = `/article/essay/essay?id=${item.id}`
				// } else {
				// 	path = `/article/essay/essay?catId=${item.id}`
				// }

				jumpLink({
					type: 1,
					path
				})
			},
			getList(catId, catSubId, len) {
				const that = this
				let ajaxData = {}
				ajaxData.content_cat_id = catId
				ajaxData.cat_id = catSubId
				that.emptyType = -1
				that.$ajax({
					url: '/api/content/',
					isLoading: false,
					ajaxData,
					type: 'GET',
					successFun(res) {
						that.list = res.list.data.slice(0, len)
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
	.com-multiline {
		.pt-20 {
			padding-top: r(20px) !important;
		}

		&_header {
			padding: r(20px);
			display: flex;
			align-items: center;
			font-size: r(24px);

			&-main-heading {
				font-size: r(28px);
				font-weight: 700;
				white-space: wrap;
				@include wordEllipsisNum(1);
			}

			&-sub-heading {
				font-size: 400;
				margin-top: r(10px);
				white-space: pre-wrap;
				@include wordEllipsisNum(1);
			}

			&-more {
				white-space: nowrap;
				flex-shrink: 0;
			}
		}

		&_body {
			padding: 0 r(20px) r(20px);

			&-item {
				display: flex;
				align-items: flex-start;
				margin-bottom: r(20px);

				&:last-child {
					margin-bottom: 0;
				}

				&-info {
					width: 100%;
					height: r(130px);
					display: flex;
					flex-direction: column;
					justify-content: space-between;
				}

				&-img {
					width: r(210px);
					height: r(130px);
					flex-shrink: 0;
					margin-right: r(24px);
					font-size: r(60px);
				}

				&-title {
					font-weight: 700;
					font-size: r(28px);
					color: #475965;
					white-space: wrap;
					@include wordEllipsisNum(2);
				}

				&-desc {
					width: 100%;
					display: flex;
					align-items: center;
					justify-content: space-between;
					color: #475965;
					font-size: r(22px);
				}
			}
		}
	}
</style>
