<template>
	<view class="videoDetail">
		<image class="back" @tap="back()" src="../../../static/images/zuoo.png" mode=""></image>
		<view class="v" :style="'top:'+moveY+'px'">
			<video :src="detail.videofile" autoplay controls></video>
		</view>
		<image src="../../../static/images/imggg.png" mode=""></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				detail:'',
				start:[0,0],
				moveY:0,
				moveX:0,
				windowWidth:'',
				windowHeight:'',
			}
		},
		onLoad(options) {
			var _this = this
			_this.getVideoDetail(options.id)
		},
		onShow() {
			const { windowWidth, windowHeight } = uni.getSystemInfoSync();  
			this.windowWidth = windowWidth
			this.windowHeight = windowHeight
		},
		methods:{
			
			
			
			back(){
				uni.navigateBack({
					delta:1,
				})
			},
			getVideoDetail(id){
				var _this = this
				_this.$http('user.video_detail',{id:id}).then(res=>{
					this.detail = res.data
				})
			}
		},
		onReachBottom(e) {
			console.log(1)
		}
	}
</script>

<style lang="less">
	.videoDetail,.v{
		width: 100vw;
		height: 100vh;
	}
	.videoDetail{
		position: relative;
		.v{
			position: absolute;
			video{
				width: 100%;
				height: 100%;
			}
		}
		.back{
			width: 30rpx;
			height: 30rpx;
			position: absolute;
			top: 35px;
			left: 30rpx;
			z-index: 10;
		}
	}
</style>
