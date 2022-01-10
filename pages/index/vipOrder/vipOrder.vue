<template>
	<view class="vipOpen">
		<view class='topBg'></view>
		<view class='pageTop' style="padding-top:20px;">
			<view class='fanhui' @tap="back1">
				<image style="width: 30rpx; height: 30rpx;" src="../../../static/images/zuo.png" mode=""></image>
			</view>
			<view class='topList'>
				同城优选
			</view>
		</view>
		<view class='user'>
			<view class='img'><image :src="user.avatar" /></view>
			<view class='name'>{{user.nickname}}</view>
		</view>
		<view class='vipCard'>
			<view class='title'>会员服务类型</view>
			<view class='card'>
				<image src='@/static/images/index/vipCardBg.png' />
				<view class='position'>
					<view class='name'>{{order.vipname}}</view>
					<view class='time'>有效期：{{order.termnum}}天</view>
					<view class='price'>¥<text>{{order.presentprice}}</text><span style="text-decoration: line-through;">¥{{order.costprice}}</span></view>
					<view class='give'>{{order.hbmsg}}</view>
				</view>
			</view>
			<view class='tip'>注: 改卡一经售出概不退换</view>
		</view>
		<view class='itemList'>
			<view class='item'>
				<view class='left'>姓名</view>
				<view class='right'>
					<u-input v-model="name" placeholder="请输入姓名" clearable="false"/>
				</view>
			</view>
			<view class='item'>
				<view class='left'>手机</view>
				<view class='right'>
					<u-input v-model="phone" placeholder="请输入联系电话" clearable="false"/>
				</view>
			</view>
		</view>
		<view class='bottom'>
			<view class='shouye'>
				合计: <text style="font-size: 24rpx;">¥</text><text style="font-size: 42rpx;">{{order.presentprice}}</text>
			</view>
			<view class='btn'>
				<view class='btntext' @tap="tijiao">立即开通</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user:'',
				order:'',
				
				name:'',
				phone:'',
			}
		},
		onLoad(options) {
			var _this = this
			_this.$http('user.info',{}).then(res=>{
				_this.user = res.data
			})
			_this.$http('user.detail',{vip_id:options.id}).then(res=>{
				_this.order = res.data
			})
		},
		methods: {
			back(){
				this.$tools.routerTo("/pages/index/index",true);
			},
			back1(){
				uni.navigateBack({
					delta:1,
				})
			},
			//提交订单
			tijiao(){
				var _this = this
				if(!_this.name || !_this.phone){
					uni.showToast({
						title:'请填补充完整信息',
						icon:'none'
					})
					return;
				}
				console.log("订单生成中")
				let data = {
					vip_id:_this.order.id,
					name:_this.name,
					phone:_this.phone,
				}
				if(uni.getStorageSync("vipid")){
					data.pid = uni.getStorageSync("vipid")
				}
				_this.$http('user.orderDetail',data).then(res=>{
					_this.$http('user.vipSn',{order_sn:res.data}).then(res=>{
						var data = res.data.pay_data;
						uni.requestPayment({
							nonceStr: data.nonceStr,　　　　　　//　　随机字符串　　　　【字符串】
							package: data.package,　　　　　　　//　　统一下单接口 必须加前缀：prepay_id=xxxxxxxx  【字符串】
							paySign: data.paySign,　　　　　　　//　　签名     【字符串】
							signType: data.signType,　　　　　　//　　签名算法  【字符串】
							timeStamp: data.timeStamp,　　　　 //　　时间戳   【字符串】
							success: function (res) {
								console.log('success:' + JSON.stringify(res));
								_this.$tools.routerTo("/pages/index/user",true);
							},
							fail: function (err) {
								console.log('支付失败')
								console.log('fail:' + JSON.stringify(err));
							},
							complete:function(res){
								console.log('xxxxxxxxxxxxxxxxxx---complete')
							}
						});
					})
				})
			},
		}
	}
</script>

<style lang="scss">
	.vipOpen{
		padding-top: 64px;
		min-height: 100vh;
		background: #fafafa;
	}
	.pageTop{
	  width: 100%;
	  padding-top: 20px;
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
	  color: #333;
	}
	image{
	  width: 100%;
	  height: 100%;
	}
	.topList{
		text-align: left;
		padding-left: 72rpx;
	}
	.user{
		padding: 0 32rpx;
		padding-top: 30rpx;
		margin-bottom: 40rpx;
		display: flex;
		align-items: top;
		.img{
			width: 86rpx;
			height: 86rpx;
			margin-right: 32rpx;
			image{
				border-radius: 50%;
			}
		}
		.name{
			font-size: 28rpx;
			color: #333;
		}
	}
	.vipCard{
		padding: 32rpx;
		font-size: 24rpx;
		.title{
			font-size: 28rpx;
			margin-bottom: 20rpx;
		}
		.card{
			width: 686rpx;
			height: 265rpx;
			margin: 0 auto;
			position: relative;
			.position{
				width: 100%;
				height: 100%;
				padding: 0 32rpx;
				line-height: 65rpx;
				position: absolute;
				top: 0;
				left: 0;
				.name{
					font-size: 28rpx;
				}
				.price{
					text{
						font-size: 32rpx;
						margin-right: 20rpx;
					}
				}
			}
		}
		.tip{
			color: #999;
			margin-top: 32rpx;
		}
	}
	.itemList{
		padding: 0 32rpx;
		background: #fff;
		&>.item:last-child{
			border-bottom: none !important;
		}
		.item{
			height: 90rpx;
			color: #333;
			font-size: 28rpx;
			display: flex;
			justify-content: space-between;
			align-items: center;
			border-bottom: 1px solid #EAEAEA;
		}
		.right{
			flex: 1;
			padding-left: 20rpx;
			input{
				text-align: right;
			}
		}
	}
	.bottom{
		font-size: 24rpx;
		color: #666;
		width: 100%;
		height: 120rpx;
		padding-left: 55rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		position: fixed;
		bottom: 0;
		z-index: 3; 
		background: #fff;
		.shouye{
			text{
				color: #FC2745;
			}
		}
		.btn{
			color: #fff;
			width: 424rpx;
			height: 100rpx;
			text-align: center;
			line-height: 100rpx;
			background: #FC2745;
		}
	}
</style>
