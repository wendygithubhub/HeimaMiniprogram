<template>
	<view v-if="goods_info.goods_name" class="goods-detail-container">
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
			<swiper-item v-for="(item, i) in goods_info.pics">
				<image :src="item.pics_big" mode="scaleToFill" @click="preview(i)"></image>
			</swiper-item>
		</swiper>
		<view class="goods-info-box">
			<!-- 商品价格 -->
			<view class="price">￥{{goods_info.goods_price}}</view>
			<!-- 信息主体 -->
			<view class="goods-info-body">
				<view class="goods-name">
					{{goods_info.goods_name}}
				</view>
				<view class="favi">
					<uni-icons type="star" size="18" color="gray"></uni-icons>
					<view>收藏</view>
				</view>
			</view>
			<view class="yf">快递：免运费</view>
		</view>
		<rich-text :nodes="goods_info.goods_introduce"></rich-text>
		<view class="goods-nav">
			<uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick" @buttonClick="btnClick"></uni-goods-nav>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				goods_info:{},
				
				// 底部导航栏
				// 左侧
				options:[{
					icon:"shop",
					text:"店铺"
				},{
					icon:"cart",
					text:"购物车",
					info:2
				}],
				// 右侧
				buttonGroup:[{
					text:"加入购物车",
					backgroundColor:"#ff0000",
					color:'#fff'
				},{
					text:'立刻购买',
					backgroundColor:"#ffa200",
					color:'#fff'
				}]
			};
		},
		
		onLoad(options){
			const goods_id = options.goods_id;
			this.getGoodsDetail(goods_id);	// 获取商品详情
		},
		
		methods:{
			async getGoodsDetail(goods_id){
				const {data:res}= await uni.$http.get('/api/public/v1/goods/detail',{goods_id});
			
				// 判断是否获取成功
				if(res.meta.status !== 200) return uni.$showMsg("数据获取失败");
				else{
					res.message.goods_introduce = res.message.goods_introduce.replace(/<img/g,'<img style = "display:block;"').replace(/webp/g,'jpg');
					this.goods_info = res.message;
				}
			},
			
			preview(i){
				uni.previewImage({
					current:i,
					urls:this.goods_info.pics.map(x=>x.pics_big)
				})
			},
			// 左侧按钮点击事件
			onClick(e){
				if(e.content.text == "购物车"){
					uni.switchTab({
						url:'/pages/cart/cart'
					})
				}
				console.log(e);
			},
			// 右侧按钮点击事件
			btnClick(){
				console.log("hi");
			},
		}
		
		
	}
</script>

<style lang="scss">
	.goods-detail-container{
		margin-bottom: 50px;
	}
	swiper{
		height: 750rpx;
		image{
			width: 100%;
			height: 100%;
		}
	}
	
	.goods-info-box{
		padding: 10px;
		padding-left: 0px;
		
		.price{
			color: #C00000;
			font-size: 18px;
			margin: 10px 0;
		}
		
		.goods-info-body{
			display: flex;
			justify-content: space-between;
			
			.goods-name{
				font-size: 13px;
				padding-right: 10px;
			}
			.favi{
				width: 120px;
				font-size: 12px;
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: center;
				border-left: 1px solid #efefef;
				color: gray;
			}
		}
		.yf{
			margin: 10px 0;
			font-size: 12px;
			color:gray
		}
	}

	.goods-nav{
		position: fixed;
		bottom:0px;
		left: 0;
		width: 100%;
	}
</style>
