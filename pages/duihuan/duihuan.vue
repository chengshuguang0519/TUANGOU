<template>
	<view class="duihuan">
		<view class='topBg'></view>
		<view class='pageTop' style="padding-top:20px;">
			<view class='fanhui' @tap="back">
				<image style="width: 30rpx; height: 30rpx;" src="@/static/images/zuoo.png" mode=""></image>
			</view>
			<view class='topList'>
				同城优选
			</view>
		</view>
		<image class="bg" src="@/static/images/duihuanBg.png" mode="widthFix"></image>
		<view class="duihuanIndex">
			<view class="card1">
				<view class="image">
					<view class="img"><image :src="user.avatar" mode=""></image></view>
					<view class="name"></view>
				</view>
				<view class="input">
					<u-input v-model="num" placeholder="请输入兑换码" clearable="false" border="true"/>
				</view>
				<view class="btn" @click="tijiao()">立即兑换</view>
			</view>
			<view class="card2">
				<rich-text :nodes="guize"></rich-text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user:'',
				num:'',
				guize:'',
			}
		},
		onLoad() {
			var _this = this;
			this.$http("user.info").then(res=>{
				_this.user = res.data
			})
			this.$http("user.ready_code").then(res=>{
				_this.guize = res.data
			})
		},
		methods: {
			back(){
				uni.navigateBack({
					delta:1,
				})
			},
			tijiao(){
				var _this = this;
				_this.$http("user.code",{
					code:_this.num,
				}).then(res=>{
					if(res.code == 1){
						uni.navigateTo({
							url:"/pages/goods/detail?id="+res.data.goods_id+"&type=code&num="+_this.num
						})
					}
				})
			},
		}
	}
</script>

<style lang="scss">
	.pageTop{
	  width: 100%;
	  padding-top: 25px;
	  position: fixed;
	  top: 0;
	  z-index: 12;
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
	  background: #EFA230;
	}
	image{
	  width: 100%;
	  height: 100%;
	}
	.topList{
		padding: 0 60rpx;
		font-size: 28rpx;
		font-weight: 100;
		color: #fff;
	}
	.duihuan{
		min-height: 100vh;
		padding-top: 64px;
		.bg{
			width: 100%;
		}
		.duihuanIndex{
			width: 100%;
			position: fixed;
			top: 0;
			padding-top: 64px;
			.card1{
				width: 686rpx;
				height: 410rpx;
				padding: 33rpx 54rpx;
				border-radius: 12rpx;
				background: #fff;
				margin: 0 auto;
				margin-top: 100rpx;
				.img{
					width: 150rpx;
					height: 150rpx;
					padding: 13rpx;
					margin: 0 auto;
					margin-top: -110rpx;
					background: #F47637;
					border-radius: 50%;
					image{
						border-radius: 50%;
					}
				}
				.input{
					margin-top: 34rpx;
					margin-bottom: 44rpx;
				}
				.btn{
					font-size: 30rpx;
					color: #FFFFFF;
					width: 431rpx;
					height: 100rpx;
					text-align: center;
					line-height: 100rpx;
					margin: 0 auto;
					border-radius: 60rpx;
					background: linear-gradient(#edbb69 0%, #f87148 99%);
				}
			}
			.card2{
				width: 686rpx;
				min-height: 268rpx;
				padding: 35rpx 45rpx;
				background: #fff;
				border-radius: 12rpx;
				margin: 0 auto;
				margin-top: 30rpx;
			}
		}
	}
</style>
