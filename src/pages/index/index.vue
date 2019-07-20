<template>
	<view>
		<view class="uni-common-mt">
			<form>
				<view class="uni-list">
					<view class="uni-list-cell">
						<view class="uni-list-cell-left">
							<view class="uni-label">图片来源</view>
						</view>
						<view class="uni-list-cell-right">
							<picker :range="sourceType" @change="sourceTypeChange" :value="sourceTypeIndex" mode="selector">
								<view class="uni-input">{{sourceType[sourceTypeIndex]}}</view>
							</picker>
						</view>
					</view>

					<view class="uni-list-cell">
						<view class="uni-list-cell-left">
							<view class="uni-label">图片质量</view>
						</view>
						<view class="uni-list-cell-right">
							<picker :range="sizeType" @change="sizeTypeChange" :value="sizeTypeIndex" mode="selector">
								<view class="uni-input">{{sizeType[sizeTypeIndex]}}</view>
							</picker>
						</view>
					</view>

					<view class="uni-list-cell">
						<view class="uni-list-cell-left">
							<view class="uni-label">数量限制</view>
						</view>
						<view class="uni-list-cell-right">
							<picker :range="count" @change="countChange" mode="selector">
								<view class="uni-input">{{count[countIndex]}}</view>
							</picker>
						</view>
					</view>
				</view>
					

				<view class="uni-list list-pd">
					<view class="uni-list-cell cell-pd">
						<view class="uni-uploader">
							<view class="uni-uploader-head">
								<view class="uni-uploader-title">点击可预览选好的图片</view>
								<view class="uni-uploader-info">{{imageList.length}}/{{ count[countIndex] }}</view>
							</view>
							<view class="uni-uploader-body">
								<view class="uni-uploader__files">
									<block v-for="(image,index) in imageList" :key="index">
										<view class="uni-uploader__file">
											<image class="uni-uploader__img" :src="image" :data-src="image" @tap="previewImage"></image>
										</view>
										<view class='del-btn' :data-index="index" @tap='deleteImage'>
											<view class='baicha'></view>
										</view>

									</block>
									<view class="uni-uploader__input-box">
										<view class="uni-uploader__input" @tap="chooseImage"></view>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
				<button type="primary" class="btn-test" @click="sedImg" form-type="submit">开始测试</button>
				
			</form>
		</view>
	</view>
</template>
<script>
	var sourceType = [
		['camera'],
		['album'],
		['camera', 'album']
	]
	var sizeType = [
		['compressed'],
		['original'],
		['compressed', 'original']
	]
	var index = 0;
	export default {
		
		data() {
			return {
				tips:'选择图片',
				title: 'choose/previewImage',
				imageList: [],
				sourceTypeIndex: 2,
				sourceType: ['拍照', '相册', '拍照或相册'],
				sizeTypeIndex: 2,
				sizeType: ['压缩', '原图', '压缩或原图'],
				countIndex: 0,
				count: [1, 2, 3, 4, 5, 6, 7, 8, 9],
				dataTest:'',
				index:0
			}
		},
		onUnload() {
			this.imageList = [],
				this.sourceTypeIndex = 2,
				this.sourceType = ['拍照', '相册', '拍照或相册'],
				this.sizeTypeIndex = 2,
				this.sizeType = ['压缩', '原图', '压缩或原图'],
				this.countIndex = 0;
		},
		onReady() {
			//this.getServerData(); 
		},
		methods: {
			sedImg(){
				uni.showLoading({
					title: "正在预测请稍后..."
				})
				uni.uploadFile({
					/*url: 'http://localhost:8080/XyyBook/userController/checkFlower.action', 
					files:this.imageList,
					filePath:"fds",
					name: 'pictureFile',
					
					success: function (uploadFileRes) {
						console.log(uploadFileRes.data);
					}*/
					url: 'http://localhost:8080/XyyBook/userController/updateInfo.action', 
					filePath: this.imageList[0], // uni.chooseImage函数调用后获取的本地文件路劲
					name:'pictureFile',     //后端通过'file'获取上传的文件对象
					
					success:(res) => {
						let tmp = JSON.parse(res.data);
						console.log(tmp);
						this.$store.commit('addResult',tmp);
						index = this.$store.getters.getListLength-1;
					},
					complete() {
						console.log(this);
						
						uni.hideLoading();
						uni.navigateTo({
							url: `./../result/result?index=`+index,
						});
					}
			
				});
			},
			startTest(){
				uni.request({
					url: 'http://192.168.1.51:8080/XyyBook/userController/checkFlower1.action',
					method:"GET",
					success: function(res) {
						console.log("成功");
						console.log(res);
					},
					fail: () => {
						this.tips = "网络错误，小程序端请检查合法域名";
					}
				});
			},
			getServerData() {
				uni.showLoading({
					title: "正在加载数据..."
				})
				uni.request({
					url: 'http://localhost:8080/XyyBook/userController/checkFlower1.action',
					data: {
						pictureFile:this.imageList,
					},
					success: function(res) {
						this.tips = "请求成功";
						console.log("成功");
						console.log(res);
					},
					fail: () => {
						console.log("请求超时!");
					},
					complete() {
						uni.hideLoading();
					}
				});
			},
			sourceTypeChange: function(e) {
				this.sourceTypeIndex = e.target.value
			},
			sizeTypeChange: function(e) {
				this.sizeTypeIndex = e.target.value
			},
			countChange: function(e) {
				this.countIndex = e.target.value;
			},
			chooseImage: async function() {
					if (this.imageList.length === this.count[this.countIndex]) {
						let isContinue = await this.isFullImg();
						console.log("是否继续?", isContinue);
						if (!isContinue) {
							return;
						}
					}
					uni.chooseImage({
						sourceType: sourceType[this.sourceTypeIndex],
						sizeType: sizeType[this.sizeTypeIndex],
						count: this.imageList.length + this.count[this.countIndex] > 9 ? 9 - this.imageList.length : this.count[this.countIndex],
						success: (res) => {
							//this.imageList = this.imageList.concat(res.tempFilePaths);
							this.imageList.push(res.tempFilePaths[0]);
						}
					})
				},
				isFullImg: function() {
					return new Promise((res) => {
						uni.showModal({
							content: `已经有${this.count[this.countIndex]}张图片了,是否清空现有图片？`,
							success: (e) => {
								if (e.confirm) {
									this.imageList = [];
									res(true);
								} else {
									res(false)
								}
							},
							fail: () => {
								res(false)
							}
						})
					})
				},
				previewImage: function(e) {
					var current = e.target.dataset.src
					uni.previewImage({
						current: current,
						urls: this.imageList
					})
				},
				//删除图片
				deleteImage: function(e) {
					var index = e.target.dataset.index;
					var that = this;
					var images = that.imageList;
					images.splice(index, 1);
					that.imageList = images;
				}
		}
	}
</script>

<style>
	.cell-pd {
		padding: 22upx 30upx;
	}

	.list-pd {
		margin-top: 50upx;
	}
	.btn-test{
		margin: 10px 10px;
	}
</style>
