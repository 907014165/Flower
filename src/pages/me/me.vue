<template>
	<view>
		<!--<uni-list>
			<uni-list-item title="来打我啊" thumb="../../static/head.png" />
			<uni-list-item :show-arrow="false" title="标题文字" />
			<uni-list-item :show-arrow="false" title="标题文字" />
			<uni-list-item :show-arrow="false" title="标题文字" />
			<uni-list-item :show-arrow="false" title="标题文字" />
		</uni-list>-->
		{{ msg }}
	</view>
</template>

<script>
	/*import uniList from '@/components/uni-list/uni-list.vue'
	import uniListItem from '@/components/uni-list-item/uni-list-item.vue'*/
	export default {
		data() {
			return {
				msg:'hhh'
			}
		},
		methods: {
			
		},
		/*components: {
			uniList,
			uniListItem
		},*/
	}
</script>

<style>
	/*page {
		display: flex;
		flex-direction: column;
		box-sizing: border-box;
		background-color: #fff
	}

	view {
		font-size: 28upx;
		line-height: inherit
	}

	.example {
		padding: 0 30upx 30upx
	}

	.example-title {
		font-size: 32upx;
		line-height: 32upx;
		color: #777;
		margin: 40upx 25upx;
		position: relative
	}

	.example .example-title {
		margin: 40upx 0
	}

	.example-body {
		padding: 0 40upx
	}*/
</style>
