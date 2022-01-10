<template>
	<view class="videoList">
		<view class='topBg'></view>
		<view class='pageTop' style="padding-top:20px;">
			<view class='fanhui' @tap="back">
				<image style="width: 30rpx; height: 30rpx;" src="../../../static/images/zuoo.png" mode=""></image>
			</view>
			<view class='topList'>
				短视频
			</view>
		</view>
		<view class="list">
			<view class="item" v-for="(item,i) in list" :key="i">
				<view class="v" @tap="goUrl('/pages/index/videoList/video_detail',item.id)">
					<!-- <video :src="item.videoimage" controls="false"></video> -->
					<image :src="item.videoimage" mode="aspectFit"></image>
					<image class="play" src="/static/images/play.png" mode=""></image>
				</view>
				<view class="title">
					{{item.name}}
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list:'',
				page:1,
				pageSize:10,
				next:true,
			}
		},
		onLoad() {
			var _this = this
			_this.getVideoList()
		},
		methods:{
			back(){
				uni.navigateBack({
					delta:1,
				})
			},
			getVideoList(){
				var _this = this
				_this.$http('user.video_list',{page:this.page}).then(res=>{
					if(this.page == 1){
						this.list = res.data.data
					}else{
						this.list = this.list.concat(res.data.data)
					}
					if(res.data.data.length>0){
						this.next = true
					}else{
						this.next = false
					}
				})
			},
			goUrl(url,id){
				uni.navigateTo({
					url:url+'?id='+id,
				})
			}
		},
		//触底加载更多
		onReachBottom(e) {
			if(this.next){
				this.page++
				this.getVideoList()
			}else{
				uni.showToast({
					title:'没有更多了',
					icon:'none',
				})
			}
		}
	}
</script>

<style lang="less">
	.pageTop{
	  width: 100%;
	  padding-top: 25px;
	  position: fixed;
	  top: 0;
	  z-index: 12;
	  background: #ff9900;
	}
	.pageTop .fanhui{
	  padding-top: 25px;
	  position: absolute;
	  left: 20rpx;
	  top: 50%;
	  transform: translateY(-50%);
	}
	.topBg{
	  width: 100%;
	  height: 20px;
	  position: fixed;
	  top: 0;
	  z-index: 13;
	}
	.topList{
	  height: 44px;
	  text-align: center;
	  line-height: 44px;
	  font-size: 32rpx;
	  font-weight: 700;
	}
	.pageTop,.topBg,.topList{
	  color: #333;
	}
	image{
	  width: 100%;
	  height: 100%;
	}
	.topList{
		padding: 0 60rpx;
		font-size: 28rpx;
		font-weight: 100;
		text-align: left;
		color: #fff;
	}
	.videoList{
		min-height: 100vh;
		background: #F2F2F2;
	}
	.list{
		padding: 20rpx;
		padding-top: 84px;
		display: flex;
		flex-wrap: wrap;
		.item{
			background: #fff;
			margin-bottom: 18rpx;
			.v{
				width: 710rpx;
				height: 399rpx;
				background: #000;
				position: relative;
				.play{
					width: 66rpx;
					height: 66rpx;
					position: absolute;
					top: 50%;
					left: 50%;
					transform: translate(-50%,-50%);
				}
			}
			.title{
				line-height: 78rpx;
				white-space: nowrap;
				text-overflow: ellipsis;
				overflow: hidden;
			}
		}
		&>.item:nth-child(2n){
			margin-right: 0;
		}
	}
</style>
