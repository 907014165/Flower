<template>
	<view>
		<uni-card
			title="测试结果"
			thumbnail="../../static/result-log.png" 
			extra="点击查看详情" 
			note="2019-7-05-test"
			>
			预测这是一朵:玫瑰花
		</uni-card>
		<uni-card v-for="(item,index) in flowerList" :key="index"
			title="测试结果"
			thumbnail="../../static/result-log.png" 
			extra="点击查看详情" 
			:note="item.result.date"
			@click="gotodetail(index)">
			预测这是一朵:{{ item.result.result }}
		</uni-card>
	</view>
</template>

<script>
	import uniCard from "@/components/uni-card/uni-card.vue"
	
	export default {
		data() {
			return {
				flowerList : [],
			}
		},
		onLoad() {
			this.flowerList = this.$store.getters.getAllResult;
		},
		methods: {
			gotodetail(index){
				uni.navigateTo({
					url: './../result/result?index='+index,
				});
			}
		},
		components: {uniCard}
	}
</script>

<style>

</style>
