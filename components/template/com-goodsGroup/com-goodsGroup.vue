<template>
	<view class="com-goodsGroup" v-if="curData.content.length">
		<com-component-style :curStyle="curData.style || null">
			<com-container-style :curStyle="curData.style || null">
				<view class="com-goodsGroup_title" v-if="curData.options.mainHeading">
					<text
						class="com-goodsGroup_title-main"
						:style="{
							color: curData.style.mainColor,
						}"
					>
						{{ curData.options.mainHeading }}
					</text>
					<text
						class="com-goodsGroup_title-sub"
						v-if="curData.options.subHeading"
						:style="{
							color: curData.style.subColor,
						}"
					>
						{{ curData.options.subHeading }}
					</text>
				</view>
				<view class="com-goodsGroup_body" :style="bodyStyle">
					<view class="com-goodsGroup_body-list" :style="listStyle">
						<view
							class="com-goodsGroup_body-list-item"
							:class="[setItemClass()]"
							v-for="item in curData.content"
							:key="item.id"
							@click="handleItem(item)"
						>
							<view
								class="goods-cover"
								:class="{
									'detail-cover': curData.options.row === -1,
								}"
							>
								<view class="goods-cover-img">
									<com-image :src="item.main_image || ''" :tr="curData.style.imgTR" :br="curData.style.imgBR" mode="aspectFill" />
								</view>
							</view>
							<view
								class="goodsInfo"
								:class="{
									'flex-goodsInfo': curData.options.row === -1,
								}"
								:style="goodsStyle"
								v-show="curData.options.showFields.length"
							>
								<view
									class="goodsInfo-title"
									:style="{
										marginTop: curData.options.row === -1 && '0px',
									}"
									v-show="getShowField('name')"
								>
									<text
										class="title-span"
										:class="{
											'ellipsis-2': getShowField('intro') && !item.intro,
										}"
									>
										{{ item.name }}
									</text>
								</view>
								<view class="goodsInfo-desc" v-show="getShowField('intro')">
									<text class="desc-span">
										{{ item.intro }}
									</text>
								</view>
								<view class="goodsInfo-price">
									<text v-show="getShowField('cost_price')">￥{{ twoDecimals(item.cost_price) }}</text>
									<view class="goodsInfo-btnImg" v-show="getShowField('btn')" @click.stop="openSku(1, item)">
										<com-image
											v-if="curData.options"
											:src="curData.options.btnImg.url || ''"
											:tr="curData.style.imgTR"
											:br="curData.style.imgBR"
											backgroundColor="transparent"
										/>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</com-container-style>
		</com-component-style>
		<middleSku ref="area" v-if="showSku" :isShow.sync="showSku" :goodsData="goods" :param="skuParam" :isShowAdd="isShowAdd"></middleSku>
	</view>
</template>

<script>
import middleSku from '@/components/sku/middle_sku.vue';
import { transformPixel, twoDecimals, jumpLink } from '../utils.js';
export default {
	name: 'comGoodsGroup',
	components: {
		middleSku,
	},
	props: {
		curData: {
			type: Object,
			default() {
				return {};
			},
		},
	},
	data() {
		return {
			listStyle: '',
			goodsStyle: '',
			bodyStyle: '',
			showSku: false,
			skuParam: {},
			isShowAdd: false,
			selectIds: [],
			input_num: {
				min_len: 1,
				max_len: 1,
				num: 1,
			},
		};
	},
	created() {
		this.setListLayout();
		this.setGoodsStyle();
		this.setBodyStyle();
	},
	methods: {
		twoDecimals,
		openSku(type, data) {
			if (data.source === 1) {
				data.stock = Number.MAX_SAFE_INTEGER;
				if (data.default_sku) {
					data.default_sku.stock = Number.MAX_SAFE_INTEGER;
				}
				if (data.sku && data.sku.length) {
					data.sku.forEach(ele => {
						ele.stock = Number.MAX_SAFE_INTEGER;
					});
				}
			}
			let that = this;
			for (let item of data.sku) {
				item.type = 0;
			}
			that.goods = data;
			let no_in_pay_car = that.goods.no_in_pay_car;
			if (no_in_pay_car == 0) {
				that.isShowAdd = false;
			} else {
				that.isShowAdd = true;
			}
			let obj = {};
			console.log(data, '当前商品');

			obj.info = that.goods;
			obj.ids = that.selectIds;
			obj.type = type;
			obj.num = that.input_num.num;
			obj.goods_id = that.goods.id;
			if (that.type == 4) {
				obj.info.group_booking_id = that.query.group_booking_id;
			}
			if (that.goods.purchase_limit_max > 0) {
				if (that.goods.purchase_limit_min > 0) {
					that.input_num.min_len = that.goods.purchase_limit_min;
					that.input_num.num = that.input_num.min_len;
				}
				if (that.goods.purchase_limit_max > 0) {
					that.input_num.max_len = that.goods.purchase_limit_max;
				}
				obj.limit = that.input_num;
			} else {
				obj.num = that.input_num.num;
			}
			obj.source = 'details';
			that.skuParam = obj;
			// this.$store.commit('setScrollBiewZIndex', 100)
			// this.$store.commit('setCurrentHandleSku', {
			// 	goods: that.goods,
			// 	skuParam: that.skuParam,
			// 	isShowAdd: that.isShowAdd
			// })
			that.showSku = true;
		},
		handleItem(item) {
			const link = {
				type: 1,
				path: `/shop/goods?id=${item.id}`,
			};
			jumpLink(link);
		},
		getShowField(field) {
			return this.curData.options.showFields.includes(field);
		},
		setListLayout() {
			const { row } = this.curData.options;
			this.listStyle = `grid-template-columns: repeat(${row !== -1 ? row : 1}, 1fr)`;
		},
		setItemClass() {
			const { row } = this.curData.options;
			return [row > 1 ? 'p-gap' : '', row === -1 && 'flex-item', (row === 2 || row === 3) && 'fixed-height'];
		},
		setGoodsStyle() {
			const { row } = this.curData.options;
			this.goodsStyle = `padding: ${row <= 1 ? `${transformPixel(8)} ${transformPixel(10)}` : ''};flex: ${row === -1 && 1};`;
		},
		setBodyStyle() {
			const { row } = this.curData.options;
			this.bodyStyle = `padding-top: ${transformPixel(10)};padding-right: ${row !== 1 ? transformPixel(8) : '0px'};`;
		},
	},
};
</script>

<style lang="scss">
.com-goodsGroup {
	position: relative;
	.p-gap {
		padding: 0 r(0px) r(20px) r(16px);
	}
	&_title {
		padding: r(20px) r(20px) 0;
		display: flex;
		align-items: center;
		&-title {
			@include wordEllipsisNum(1);
		}
		&-main {
			display: inline-block;
			font-size: 28rpx;
			color: #475965;
			font-weight: 700;
		}
		&-sub {
			position: relative;
			display: inline-block;
			// height: 30rpx;
			font-size: 22rpx;
			color: #475965;
			font-size: 400;
			padding-left: 15rpx;
			margin-left: 15rpx;
			&::before {
				position: absolute;
				content: '';
				top: 50%;
				left: -0.5px;
				width: 1rpx;
				height: 48rpx;
				background-color: #475965;
				transform: translateY(-50%) scale(0.5);
			}
		}
	}
	&_body {
		padding-top: r(20px);
		&-list {
			display: grid;
			&-item {
				display: flex;
				flex-direction: column;
				&.fixed-height {
					.goodsInfo-title {
						height: r(35px);
					}
					.goodsInfo-desc {
						height: r(32px);
						line-height: r(24px);
					}
				}
				&.flex-item {
					flex-direction: row;
					padding: 0 0 r(20px) r(20px);
					.goods-cover {
						border-radius: r(10px) !important;
					}
				}
				.goods-cover {
					position: relative;
					font-size: r(40px);
					width: 100%;
					height: 0;
					padding-bottom: 100%;
					overflow: hidden;
					&.detail-cover {
						width: r(204px);
						height: r(204px);
						padding-bottom: 0;
					}
					&-img {
						position: absolute;
						top: 0;
						left: 0;
						width: 100%;
						height: 100%;
					}
				}
				.goodsInfo {
					font-size: r(26px);
					color: #333;
					&.flex-goodsInfo {
						display: flex;
						flex-direction: column;
						padding-bottom: 0 !important;
						padding-right: 0 !important;
						.goodsInfo-price {
							flex: 1;
							align-items: flex-end;
						}
					}
					&-title {
						margin-top: r(10px);
						.title-span {
							@include wordEllipsisNum(1);
							&.ellipsis-2 {
								@include wordEllipsisNum(2);
							}
						}
					}
					&-desc {
						color: #666;
						font-size: r(24px);
						padding-top: r(10px);
						.desc-span {
							@include wordEllipsisNum(1);
						}
					}
					&-price {
						color: #d33826;
						font-size: r(28px);
						font-weight: 600;
						display: flex;
						align-items: center;
						justify-content: space-between;
						margin-top: r(20px);
					}
					&-btnImg {
						width: r(36px);
						height: r(36px);
					}
				}
			}
		}
	}
}
</style>
