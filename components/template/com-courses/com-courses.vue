<template>
	<view class="com-courses" v-if="list.length">
		<com-component-style :curStyle="curData.style || null">
			<com-container-style :curStyle="curData.style || null">
				<view
					class="com-courses_header"
					:style="{
						paddingLeft:
						  curData.options.titleIcon && curData.options.titleIcon.url
							? '70rpx'
							: '20rpx'
					}"
					v-if="curData.options.mainHeading"
					@click="handleJump"
				>
					<view
						class="com-courses_header-icon"
						v-if="
						  curData.options.titleIcon && curData.options.titleIcon.url
						"
					>
						<com-image :src="curData.options.titleIcon.url" />
					</view>
					<view style="flex: 1">
						<view
							class="com-courses_header-main-heading"
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
							class="com-courses_header-sub-heading"
							:style="{
								color: curData.style.subColor,
								fontSize: transformPixel(
									curData.style.sfs || 12
								)
							}"
							v-if="curData.options.subHeading"
						>
							{{ curData.options.subHeading }}
						</view>
					</view>
					<view class="com-courses_header-more"
						v-if="curData.options.showMoreBtn"
						@click="handleMoreBtn(curData.options.moreLink)"
					>
						<view class="">
							查看更多
						</view>
						<view class="com-courses_header-more-icon bg-cover">
							
						</view>
					</view>
				</view>
				<view
					class="com-courses_body"
					:class="{
						'pt-20': !curData.options.mainHeading
					}"
				>
					<view
						class="com-courses_body-item"
						v-for="item in list"
						:key="item.id"
						@click="handleItem(item)"
					>
						<view class="com-courses_body-item-img">
							 <view class="com-courses_body-item-img-inner">
								<com-image
									:src="item.cover_url || ''"
									:tr="curData.style.imgTR"
									:br="curData.style.imgBR"
									mode="aspectFill"
								/>
							</view>
						</view>
						<view class="com-courses_body-item-info">
							<view class="com-courses_body-item-title">
								{{ item.name }}
							</view>
							<view class="com-courses_body-item-desc">
								{{item.sections_count}}章/{{item.user_study_count}}次已学
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
		name: 'ComCourses',
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
			if (options.source === 1) {
				this.getList(options.row)
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
				jumpLink({
					type: 1,
					path: `/subPackages/course/detail?id=${item.id}`
				})
			},
			getList(len) {
				const that = this
				let ajaxData = {
					page_size: len
				}
				// that.$ajax({
				// 	url: '/api/college/courses',
				// 	isLoading: false,
				// 	ajaxData,
				// 	type: 'GET',
				// 	successFun(res) {
				// 		that.list = res.list.data.slice(0, len)
				// 	},
				// 	errorFun: function (error) {
				// 		uni.tools.alert.error(error.error_msg)
				// 	}
				// })
			},
			handleMoreBtn(link) {
				if (link.type === 99) {
					uni.$emit('changeTabbar', link.navIndex)
					return
				}
				jumpLink(link)
			}
		}
	}
</script>

<style lang="scss">
	.com-courses {
		.pt-20 {
			padding-top: r(20px) !important;
		}

		&_header {
			position: relative;
			padding: r(20px) r(20px) r(20px) r(70px);
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
				display: flex;
				align-items: center;
				white-space: nowrap;
				flex-shrink: 0;
				&-icon{
					width: r(30px);
					height: r(30px);
					background-image: url(ossImg('shumiao/common/arrow-right2.png'));
					margin-left: r(5px);
				}
			}
			
			&-icon {
				position: absolute;
				left: 20rpx;
				top: 24rpx;
				width: 40rpx;
				height: 40rpx;
			}
		}

		&_body {
			padding: 0 20rpx 20rpx;
			display: grid;
			gap: 30rpx 20rpx;
			grid-template-columns: repeat(2, 1fr);
			width: 100%;
			&-item {
				width: 100%;
				display: flex;
				flex-direction: column;
				align-items: flex-start;

				&:last-child {
					margin-bottom: 0;
				}

				&-info {
					width: 100%;
					display: flex;
					flex-direction: column;
					justify-content: space-between;
				}

				&-img {
					position: relative;
					width: 100%;
					padding-bottom: 80%;
					display: flex;
					align-items: center;
					flex-shrink: 0;
					margin-right: r(24px);
					font-size: r(60px);
					margin-bottom: 10rpx;
					&-inner {
						position: absolute;
						width: 100%;
						height: 100%;
						top: 0;
						left: 0;
						z-index: 1;
					}
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
