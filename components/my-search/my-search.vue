<template>
	
	<view class="search-container">
		<view class="search-box" @click="switchSearch">
			<uni-icons type="search" size="17"></uni-icons>
			<view class="placeholder">搜索</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"my-search",
		data() {
			return {
				
			};
		},
		methods:{
			switchSearch(){
				uni.navigateTo({
					url:'/subpkg/search/search',
				})
			}
		}
	}
</script>

<style lang="scss">
.search-container{
	background-color: #C00000;
	padding: 0rpx 10px ;
	height: 50px;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;

	.search-box{
		width: 100%;
		background-color: #fff;
		border-radius: 15px;
		height: 36px;
		display: flex;
		justify-content: center;
		align-items: center;
		.placeholder{
			font-size: 15px;
			margin-left: 5px;
		}
	}
}
</style>
