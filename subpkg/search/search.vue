<template>
	<view class="container">
		<uni-search-bar focus="true" :radius="15" cancelButton="none" @input="input"></uni-search-bar>
		<!-- 搜索建议 -->
		<view class="sugg-list" v-if="searchResults.length!== 0">
			<!-- 标题 -->
			<view class="sugg-item" v-for="(item, i) in searchResults" :key = "i" @click="gotoDetail(item.goods_id)">
				<view class="sugg-title">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>
		
		<!-- 搜索历史 -->
		<view class="history-box" v-else>
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17" @click="cleanHistorys"></uni-icons>
			</view>
			<!-- 内容 -->
			<view class="history-list">
				<uni-tag :text="item" v-for="(item, i) in historys" :key = "i" @click="gotoGoodsList(item)"></uni-tag>
			</view>
			
		</view>
	</view>
	
	
</template>

<script>
	export default {
		data() {
			return {
				timer:null,
				searchResults:[],
				historyList:['a', 'app','hi'],
			};
		},
		
		methods:{
			onLoad(){
				console.log('onload');
				this.historyList = JSON.parse(uni.getStorageSync('kw')||'[]')
			},
			
			cleanHistorys(){
				this.historyList = [];
				uni.setStorageSync('kw', []);
			},
			
			input(e){
				clearTimeout(this.timer);
				this.timer = setTimeout(()=>{
					this.kw = e.value;
					this.getSearchList();
				},500)
			},
			
			async getSearchList(){
				console.log(this.kw);
				if(this.kw === ''){
					this.searchResults = [];
					return;
				}
				
				const {data:res} = await uni.$http.get('/api/public/v1/goods/qsearch',{query:this.kw})
				if(res.meta.status !== 200) return uni.$showMsg(服务器故障);
				this.searchResults = res.message;
				
			},
			
			// 保存到搜索历史historyList中
			saveSearchList(kw){
				// 将现有搜索历史转化为set
				const historySet = new Set(this.historyList)
				historySet.delete(kw); // 删掉当前输入
				historySet.add(kw); // 添加当前输入
				this.historyList = Array.from(historySet);
				uni.setStorageSync('kw', JSON.stringify(this.historyList))
			},
			
			gotoDetail(goods_id){
				uni.navigateTo({
					url:'../goods_detail/goods_detail?goods_id='+goods_id
				})
				this.saveSearchList(this.kw);
			},
			
			gotoGoodsList(kw){
				console.log('goto');
				uni.navigateTo({
					url:'../goods_list/goods_list?query=' + kw
				})
			},
			
		},
	
		computed:{
			historys(){
				return [...this.historyList].reverse()
			}
		},
		
		
	}
</script>

<style lang="scss">
	.sugg-list{
		padding: 0 5px;
		
		.sugg-item{
			padding: 13px 0px;
			font: 12px;
			border-bottom: 1px solid #efefef;
			display: flex;
			flex-direction: row;
			justify-content: space-between;
			align-items: center;
			
			.sugg-title{
				white-space: nowrap;
				overflow: hidden;
				text-overflow: ellipsis;
				margin-right: 3px;
			}
		}
	}
	
	.history-box{
		padding: 0 5px;
		
		.history-title{
			display: flex;
			justify-content: space-between;
			align-items: center;
			height: 40px;
			font-size: 13px;
			border-bottom: 1px solid #efefef;
		}
		
		.history-list{
			display: flex;
			flex-wrap: wrap;
			
			.uni-tag{
				margin-top: 5px;
				margin-right: 5px;
			}
		}
	}
</style>
