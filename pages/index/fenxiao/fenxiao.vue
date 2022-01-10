<template>
	<view class="vipOpen">
		<!-- <view class="banner">
			<image src="../../../static/images/imgg.png" mode=""></image>
		</view> -->
		<view class="title">
			填写分销人信息
		</view>
		<view class="form">
			<view class="item">
				<view class="label">
					姓名
				</view>
				<view class="input">
					<u-input v-model="name" :type="type" :border="border" />
				</view>
			</view>
			<view class="item">
				<view class="label">
					手机号
				</view>
				<view class="input">
					<u-input v-model="phone" :type="number" :border="border" />
				</view>
			</view>
			<view class="item">
				<view class="label">
					身份证号
				</view>
				<view class="input">
					<u-input v-model="cardNum" :type="type" :border="border" />
				</view>
			</view>
		</view>
		<view class="upload">
			<view class="upLabel">
				身份证正面
			</view>
			 <view class="upView" @click="chooseTheImg1">
				<image :src="fileList1.length>0 ? fileList1[0] : '../../../static/images/card2.png'" mode="widthFix"></image>
			 </view>
		</view>
		<view class="upload">
			<view class="upLabel">
				身份证反面
			</view>
			 <view class="upView" @click="chooseTheImg2">
				<image :src="fileList2.length>0 ? fileList2[0] : '../../../static/images/card1.png'" mode="widthFix"></image>
			 </view>
		</view>
		<view class="btn">
			<u-button @click="uploadTheImg">提交</u-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				action: 'http://www.example.com/upload',
				fileList1: [],
				fileList2: [],
				url1:'',
				url2:'',
				
				name:'',
				phone:'',
				cardNum:'',
			}
		},
		methods: {
			upimg(i,j){
				console.log(i,j)
			},
			//选择图片
			chooseTheImg1(){
				var _this = this;
				uni.chooseImage({
					count: 1,  //图片可选择数量
					sizeType: ['original','compressed'],  //original 原图，compressed 压缩图，默认二者都有
					//sourceType: ['album'],   //album 从相册选图，camera 使用相机，默认二者都有。
					success: res => {
						let imgFiles = res.tempFilePaths   //图片的本地文件路径列表
						_this.fileList1 = imgFiles
					}
				})
			},
			chooseTheImg2(){
				var _this = this;
				uni.chooseImage({
					count: 1,  //图片可选择数量
					sizeType: ['original','compressed'],  //original 原图，compressed 压缩图，默认二者都有
					//sourceType: ['album'],   //album 从相册选图，camera 使用相机，默认二者都有。
					success: res => {
						let imgFiles = res.tempFilePaths   //图片的本地文件路径列表
						_this.fileList2 = imgFiles
					}
				})
			},
			//上传图片
			uploadTheImg(imgFiles){
				var _this = this;
				if(!_this.name || !_this.phone || !_this.cardNum){
					uni.showToast({
						title:'请填写完整信息',
						icon:'none',
					})
				}
				_this.$http('user.info',{}).then(ress=>{
					if(ress.data.is_dealer == 1){
						uni.showToast({
							title: '您已申请过分销人',
							icon: 'none',
						})
						return;
					}
					uni.uploadFile({
						url: `${_this.$API_URL} + index/upload`,   //后端用于处理图片并返回图片地址的接口
						filePath: _this.fileList1[0],
						name: 'file',
						success: res => {
							let data=JSON.parse(res.data)   //返回的是字符串，需要转成对象格式，打印data如下图
							_this.url1 = data.data.url
						},
						fail: () => {}
					})
					uni.uploadFile({
						url: `${_this.$API_URL} + index/upload`,   //后端用于处理图片并返回图片地址的接口
						filePath: _this.fileList2[0],
						name: 'file',
						success: res => {
							let data=JSON.parse(res.data)   //返回的是字符串，需要转成对象格式，打印data如下图
							_this.url2 = data.data.url
						},
						fail: () => {}
					})
					setTimeout(function(){
						_this.$http(
							'common.addDealer',
							{
								uid:ress.data.id,
								name:_this.name,
								phone:_this.phone,
								cardnum:_this.cardNum,
								zcard:_this.url1,
								fcard:_this.url2,
							}
						).then(res => {
							if(res.code == 0){
								uni.showToast({
									title:res.msg,
									icon:'none',
								})
							}else if(res.code == 1){
								uni.showToast({
									title:"申请已提交, 等待管理员审核",
									icon:'none',
								})
								uni.reLaunch({
									url:'/pages/index/user'
								})
							}
						});
					},500)
				})
			},
			back(){
				this.$tools.routerTo("/pages/index/index",true);
			},
		}
	}
</script>

<style lang="scss">
	image{
		width: 100%;
		height: 100%;
	}
	.banner{
		width: 100%;
		height: 200rpx;
	}
	.title{
		font-size: 28rpx;
		color: #333;
		text-align: center;
		line-height: 110rpx;
	}
	.item{
		margin: 0 32rpx;
		margin-bottom: 30rpx;
		display: flex;
		background: #F7F8F9;
		.label{
			width: 150rpx;
			line-height: 80rpx;
			font-size: 26rpx;
			color: #666;
		}
		.input{
			flex: 1;
			line-height: 80rpx;
		}
	}
	.btn{
		padding: 0 32rpx;
		margin: 32rpx 0;
	}
	.upLabel{
		color: #333;
		font-size: 26rpx;
		line-height: 110rpx;
	}
	.upload{
		padding: 0 32rpx;
		.upView{
			width: 686rpx;
		}
	}
</style>
