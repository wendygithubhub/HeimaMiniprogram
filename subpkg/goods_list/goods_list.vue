<template>
	<view>
		<view class="goods-list">
			<block v-for="(goods, i) in goodsList" :key="i">
				<view class="goods-item">
					<!-- 左侧 -->
					<view class="goods-item-left">
						<image :src="goods.goods_small_logo || defaultPic" class="goods-item-img"></image>
					</view>
					<view class="goods-item-right">
						<view class="goods-item-title">{{goods.goods_name}}}</view>
						<view class="goods-item-price">{{goods.goods_price}}
						</view>
					</view>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				queryObj:{
					//查询关键词
					query:'',
					// 商品分类id
					cid:'',
					// 页码值
					pagenum:1,
					pagesize:10,
				},
				// 商品清单
				goodsList:[],
				// 用于分页
				total:0,
				defaultPic:'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
				
			};
		},
		
		methods:{
			onLoad(options){
				this.queryObj.query = options.query || '';
				this.queryObj.cid = options.cid || '';
				this.getGoodsList();
			},
			async getGoodsList(){
				const {data:res} = await uni.$http.get('/api/public/v1/goods/search',this.queryObj)
				console.log(res);
				if(res.meta.status !== 200) return uni.$showMsg("数据获取失败");
				else{
					this.goodsList = res.message.goods;
					this.total = res.message.total;
				}
			}
		}
	}
</script>

<style lang="scss">
	.goods-list{
		.goods-item{
			display: flex;
			margin: 10px 5px;
			border: solid 1rpx #efefef;
			.goods-item-left{
				.goods-item-img{
					width: 100px;
					height: 100px;
					display: block;
					margin-right:5px;
				}
			}
			.goods-item-right{
				display: flex;
				flex-direction: column;
				justify-content: space-between;
				.goods-item-title{
					font-size: 13px;
				}
				.goods-item-price{
					font-size: 16px;
					color: #C00000;
				}
			}
		}
	}
</style>
