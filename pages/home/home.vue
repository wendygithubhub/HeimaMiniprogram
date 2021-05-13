<template>
	<view>
		<!-- 轮播图区域 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item,i) in swiperList" key="i">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<image :src="item.image_src" :mode="scaleToFill"></image>
				</navigator>
			</swiper-item>
		</swiper>

		<!-- 首页分类选项 -->
		<view class="navList">
			<view v-for="(item,i) in navList" class="navItem" @click="navClickHandle(item)">
				<image :src="item.image_src"></image>
			</view>
		</view>

		<!-- 楼层选项 -->
		<view class="floorList" v-for="(item,i) in floorList">
			<view class="floor-item">
				<view class="floor-top">
					<image :src="item.floor_title.image_src" class="floor-box-title"></image>
				</view>
				<view class="floor-bottom">
					<navigator :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src"
							:style="{width:item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
					</navigator>
					<view class="floor-img-right">
						<view v-for="(item1,i1) in item.product_list" v-if="i1 != 0">
							<navigator :url="item1.url">
								<image :src="item1.image_src" :style="{width:item1.image_width + 'rpx'}" mode="widthFix">
								</image>
							</navigator>
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperList: [],
				navList: [],
				floorList: [],
			};
		},
		onLoad() {
			this.getSwiperList();
			this.getNavList();
			this.getFloorList();
		},
		methods: {
			// 获取轮播数据
			async getSwiperList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/swiperdata');
				console.log(res);
				if (res.meta.status !== 200) {
					return uni.$showMsg('请求失败！')
				}
				this.swiperList = res.message;
			},

			// 获取分类数据
			async getNavList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/catitems');
				console.log(res);
				if (res.meta.status !== 200) {
					return uni.$showMsg('请求失败！')
				}
				this.navList = res.message;
			},

			// 获取楼层数据
			async getFloorList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/floordata');
				console.log(res);
				if (res.meta.status !== 200) {
					return uni.$showMsg('请求失败！')
				}
				// 增加url
				res.message.forEach(floor => {
					floor.product_list.forEach(prod => {
						prod.url = '/subpkg/floor_detail/floor_detail?' + prod.navigator_url.split(
							'?')[1];
					})
				})

				this.floorList = res.message;
			},

			navClickHandle(item) {
				// console.log(item.name);
				if (item.name == "分类") {
					uni.switchTab({
						url: '/pages/cate/cate',
					})
				}
			}
		}


	}
</script>

<style lang="scss">
	swiper {
		height: 330rpx;

		.swiper-item,
		image {
			width: 100%;
			height: 100%;
		}
	}

	.navList {
		width: 100%;
		margin: 15px 0px;
		display: flex;
		justify-content: space-around;

		.navItem,
		image {
			width: 128rpx;
			height: 140rpx;
		}
	}

	.floorList {
		.floor-box-title {
			height: 60rpx;
			display: flex;
		}

		.floor-bottom {
			margin-left: 10rpx;
			display: flex;
		}

		.floor-img-right {
			display: flex;
			flex-wrap: wrap;
			justify-content: space-around;
		}
	}
</style>
