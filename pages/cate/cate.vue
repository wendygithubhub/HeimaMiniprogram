<template>

	<view class="container" style="flex-direction: column;">
		<my-search></my-search>
		<view  class="container">
			<scroll-view scroll-y="true" :style="{height:wh+'px'}" class="left-scroll-list">
				<view v-for="(item, i) in cateList">
					<view :class="['left-scroll-item',i === active?'active':'']" @click="activeChange(i)">
						{{item.cat_name}}
					</view>
				</view>
			</scroll-view>

			<scroll-view scroll-y="true" :style="{height:wh+'px'}" class="right-scroll-list" :scroll-top="{scrollTop}">
				<view class="right-scroll-item" v-for="(item, i) in level2List">
					<view class="right-scroll-title">/{{item.cat_name}}/</view>
					<view class="right-scroll-content">
						<view class="right-content-image" v-for="(item2, i2) in item.children"
							@click="navigateToGoods(item2)">
							<image :src="item2.cat_icon"></image>
							<text>{{item2.cat_name}}</text>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				wh: 0,
				cateList: [],
				level2List: [],
				scrollTop: 0,
				active: 0,
			};
		},

		onLoad() {
			this.getSystemInfoFunc();
			this.getCateList();
		},

		methods: {
			// 获取屏幕高地
			getSystemInfoFunc() {
				const sysInfo = uni.getSystemInfoSync();
				// console.log(sysInfo);
				this.wh = sysInfo.windowHeight;
			},
			// 获取分类数组
			async getCateList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/categories');
				if (res.meta.status !== 200) {
					return uni.$showMsg('请求失败！')
				}
				this.cateList = res.message;
				this.level2List = res.message[0].children;
			},
			// 分类选项改变
			activeChange(i) {
				this.active = i;
				// 获取二级标题
				this.level2List = this.cateList[i].children;
				this.scrollTop = this.scrollTop === 0 ? 1 : 0;
			},
			//商品页面跳转
			navigateToGoods(item2) {
				uni.navigateTo({
					url: '../../subpkg/goods_detail/goods_detail?cid=' + item2.cat_id,
				})
			},

		}


	}
</script>

<style lang="scss">
	.container {
		display: flex;

		.left-scroll-list {
			width: 120px;

			.left-scroll-item {
				line-height: 60px;
				text-align: center;
				background-color: #f7f7f7;
				font-size: 12px;

				&.active {
					background-color: #fff;
					position: relative;

					&::before {
						display: block;
						content: '';
						background-color: #c00000;
						width: 3px;
						height: 30px;
						position: absolute;
						top: 25%;
					}
				}
			}
		}

		.right-scroll-list {
			.right-scroll-title {
				font-size: 12px;
				font-weight: bold;
				padding: 15px, 0;
				text-align: center;
			}

			.right-scroll-content {
				width: 100%;
				display: flex;
				flex-wrap: wrap;

				.right-content-image {
					width: 33.33%;
					display: flex;
					flex-direction: column;
					align-items: center;
					justify-content: center;

					image {
						width: 60px;
						height: 60px;
					}

					text {
						font-size: 10px;
					}
				}

			}
		}


	}
</style>
