<template>
	<view>
		<view class="topBg">
			<view class="title">兑积分赢好礼</view>
			<view class="num">当前积分  <span>{{user.score}}</span></view>
		</view>
		<view class="list">
			<!-- <view class="item" v-for="(item,i) in list" :key="i">
				<view class="left">
					<image src="../../../static/images/youhui.png" mode=""></image>
					<span></span>
				</view>
				<view class="center">
					<view class="top">折扣卷 <span>八折</span></view>
					<view class="bottom">可用于全部商品</view>
				</view>
				<view class="right">
					<view class="top">60积分</view>
					<view class="btn" @click="duihuan(item.id)">兑换</view>
				</view>
			</view> -->
			<view class="coupon-list" v-for="(c, index) in list" :key="index" @tap="toCouponDetail(c)">
				<shopro-coupon :state="stateCurrent" type="1" :couponData="c" @userinfo="userinfo"></shopro-coupon>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user:'',
				list:'',
				stateCurrent: 0,
				couponList: []
			}
		},
		onLoad() {
			this.$http("user.info",{}).then(res=>{
				this.user = res.data
			})
			this.getList()
		},
		methods: {
			userinfo(e){
				this.user = e
			},
			onNav(id) {
				if (this.stateCurrent !== id) {
					this.stateCurrent = id;
					this.couponList = [];
					this.getCouponList();
				}
			},
			getList(){
				var _this = this;
				this.$http("common.youhuiindex",{type:4}).then(res=>{
					this.list = res.data
				})
			},
			duihuan(id){
				this.$http("common.youhuiscore",{id:id}).then(res=>{
					console.log(res)
				})	
			},
			//跳转优惠券详情
			toCouponDetail(data) {
				if (data.user_coupons_id) {
					this.jump('/pages/app/coupon/detail', { id: data.id, userCouponId: data.user_coupons_id });
				} else {
					this.jump('/pages/app/coupon/detail', { id: data.id });
				}
			},
			jump(path, parmas) {
				this.$Router.push({
					path: path,
					query: parmas
				});
			},
		}
	}
</script>

<style lang="scss">
	image{
		width: 100%;
		height: 100%;
	}
	.topBg{
		color: #FFFFFF;
		height: 258rpx;
		padding-top: 60rpx;
		text-align: center;
		background: #ffa604;
		.title{
			font-size: 72rpx;
			font-weight: 700;
			margin-bottom: 20rpx;
		}
		.num{
			font-size: 24rpx;
			display: inline-block;
			padding: 8rpx 39rpx;
			background: #B14013;
			border-radius: 17rpx;
		}
	}
	.list{
		padding: 0 20rpx;
		padding-top: 30rpx;
		.item{
			box-sizing: border-box;
			width: 710rpx;
			height: 175rpx;
			margin: 0 auto;
			margin-bottom: 30rpx;
			padding-left: 45rpx;
			display: flex;
			align-items: center;
			justify-content: space-between;
			box-shadow: 1px 1px 10px #EF8358;
			border-top-right-radius: 20rpx;
			border-bottom-right-radius: 20rpx;
			.left{
				width: 71rpx;
				height: 55rpx;
				position: relative;
				span{
					display: inline-block;
					width: 37rpx;
					height: 37rpx;
					border-radius: 50%;
					background: rgba(237,170,143,0.6);
					position: absolute;
					right: -13rpx;
					bottom: -13rpx;
				}
			}
			.center{
				flex: 1;
				text-align: center;
				.top{
					font-size: 40rpx;
					margin-bottom: 30rpx;
					span{
						color: #A77411;
						margin-left: 30rpx;
					}
				}
				.bottom{
					font-size: 24rpx;
				}
			}
			.right{
				width: 160rpx;
				height: 100%;
				text-align: center;
				padding-top: 35rpx;
				background: #EF8358;
				border-top-right-radius: 20rpx;
				border-bottom-right-radius: 20rpx;
				.top{
					text-align: center;
					color: #333;
					margin-bottom: 23rpx;
				}
				.btn{
					color: #FFFFFF;
					width: 120rpx;
					height: 50rpx;
					margin: 0 auto;
					line-height: 50rpx;
					text-align: center;
					border-radius: 100rpx;
					background-color: #B14013;
				}
			}
		}
	}
</style>
