<template>
	<view class="com-article-card">
		<view class="title">
			<view class="navTitle" v-if="datas.modules_name || datas.modules_subheading">
				<text v-if="datas.modules_name">{{datas.modules_name}}</text>
				<text class="subHeading" v-if="datas.modules_subheading">{{datas.modules_subheading}}</text>
			</view>
			<text class="desc" @click="checkMore(datas)">查看更多</text>
		</view>
		<view class="article-list-item" v-for="item in datas.data" @click="godetail(datas,item.id)" :key="item.id">
			<view :style="{'backgroundImage': `url(${item.cover })`}" class="cover"></view>

			<view class="name-desc">
				<!-- <text>{{item.title}}</text> -->
				<view class="name">
					{{item.title}}
				</view>

			</view>
		</view>

	</view>
</template>

<script>
	export default {
		props: {
			datas: {
				type: Object,
				default: {}
			}
		},
		data() {
			return {
				multiTextData: {},
			}
		},
		methods: {
			godetail(item, id) {
				if (item.skip_type == 0) {
					this.toUrl('/article/essay/essay', {
						id
					})
				}
				if (item.skip_type == 1) {
					this.toUrl('/article/essay/essay', {
						catId: id
					})
				}
			},
			checkMore(item) {
				console.log(item, '看看');
				// 跳转文章
				if (item.skip_type == 0) {
					uni.navigateTo({
						url: `/articleList/articleList`
					})
				}
				// 跳转分类 
				else if (item.skip_type == 1) {
					let catId = item.cate_value
					let catSubId = item.sub_cate_value
					this.toUrl('/categoryList/categoryList', {
						catId,
						catSubId
					})
					// uni.navigateTo({
					// 	// url: `/categoryList/categoryList/?content_cat_id=${item.cate_value}&cat_id=${item.sub_cate_value}`
					// 	url: '/categoryList/categoryList/?content_cat_id=' + item.cate_value + '&cat_id=' + item
					// 		.sub_cate_value
					// 	// url: '/categoryList/categoryList/?content_cat_id=' + item.cate_value
					// 	// url: '/categoryList/categoryList'
					// })

				}
			}
		},
		mounted() {}
	}
</script>

<style lang="scss" scoped>
	.com-article-card {
		width: 100%;
		background: #FFFFFF;
		box-shadow: 0px 4px 10px 0px rgba(212, 212, 212, 0.3);
		border-radius: r(20px);
		padding: r(30px);
		margin-left: 50%;
		transform: translateX(-50%);

		.title {
			display: flex;
			align-items: center;
			justify-content: space-between;
			color: #000;
			font-size: r(36px);
			font-weight: 500;
			margin-bottom: r(30px);

			.navTitle {
				font-size: r(32px);
				font-weight: bold;

				.subHeading {
					display: inline-block;
					border-left: r(1px) solid #333;
					line-height: r(5px);
					margin: r(10px);
					padding: r(15px);
					font-size: r(26px);
					font-weight: 400;
				}
			}


			.desc {
				flex-shrink: 0;
				color: #999999;
				font-size: r(28px);
				padding-right: r(22px);
				background-image: url(bg('images/selmac/arrow-right.png'));
				background-size: r(13px) r(18px);
				background-repeat: no-repeat;
				background-position: right center;
			}
		}

		.article-list-item {
			display: flex;
			align-items: center;
			margin-bottom: r(40px);

			.cover {
				width: r(205px);
				height: r(134px);
				border-radius: r(10px);
				margin-right: r(16px);
				flex-shrink: 0;
				background-size: cover;
				background-position: center;
				background-repeat: no-repeat;
			}

			.name-desc {
				height: r(134px);
				display: flex;
				// flex-direction: column;
				align-items: flex-start;
				justify-content: center;
				color: #000;
				font-size: r(32px);
				margin-bottom: r(8px);

				.name {
					@include wordEllipsisNum(2);
				}

				.desc {
					color: #666;
					font-size: r(24px);
				}
			}
		}
	}
</style>