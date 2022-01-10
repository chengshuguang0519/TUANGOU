<template>
	<view class="vipOpen">
		<view class='topBg'></view>
		<view class='pageTop' style="padding-top:20px;">
			<view class='fanhui' @tap="back">
				<image style="width: 30rpx; height: 30rpx;" src="../../../static/images/zuoo.png" mode=""></image>
			</view>
			<view class='topList'>
				同城惠享
			</view>
		</view>
		<view class='topBanner'>
			<image class="img" src='@/static/images/imggg.png' />
			<view class='userCard'>
				<image src='@/static/images/vipBg.png' />
				<view class='cardValue'>
					<view class="title">
						<view class="">同城惠享</view>
						<view class=""> <span>{{activeData.vipname}}</span> </view>
					</view>	
					<view class="user">
						<view class="userImg"><image :src="user.avatar" mode=""></image></view>
						<view class="userInfo">
							<view class="userName">{{user.nickname}}</view>
							<view class="userStatic">
								<span v-if="user.is_vip == 0">暂未加入会员</span>
								<span v-if="user.is_vip == 1">已加入会员</span>
							</view>
						</view>
						<view class="vipNum">
							<view class="num">{{activeData.enjoyzk}}人</view>
							<view class="">已加入会员</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		<view class='vipPayList'>
			<view :class="active == i ? 'active item' : 'item' " v-for="(item,i) in detail" :key="i" @tap="cardTab(item,i)">
				<view class='v1'>
					<view class="name">{{item.vipname}}</view>
					<view class="img"><image v-if="active == i" src='@/static/images/right.png' /></view>
				</view>
				<view class='v2'>有效期: {{item.termnum}}天</view>
				<view class='v3'><text> <text class="rmb">¥</text>{{item.presentprice}}<text class="price">¥{{item.costprice}}</text></text></view>
				<view class='v4'>
					<view class='img'><image src='@/static/images/why.png' /></view>
					<view class='text'>赠送3个5元红包，5个2元红包···</view>
				</view>
			</view>
		</view>
		<view class='imgText' v-if="activeData.jsimage">
			<image :src="activeData.jsimage" mode="widthFix"></image>
			<!-- <view class='item'>
				<view class='img'><image src='@/static/images/imggg.png' /></view>
				<view class='text'>
					<view class='name'>自买省钱</view>
					<view class='title'>自买可获得优惠</view>
				</view>
			</view> -->
		</view>
		<view class='vipRed'>
			<view class='title'>
				会员专属红包
				<view class='titleBg'></view>
			</view>
			<view class='list'>
				<view class="hoList" v-if="activeData.hbarr.length<=0" style="line-height: 40rpx;text-align: center;color: #666;">
					暂无会员红包
				</view>
				<view class='item' v-for="(item,i) in activeData.hbarr" :key="i">
					<view class='left'>
						<view >
							<view >¥<text>{{item.amount}}</text></view>
							<view >{{item.moneymsg}}</view>
						</view>
					</view>
					<view class='right'>
						{{item.typemsg}}
						<view >
							<text>会员专属</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- <view class='vipOnly'>
			<view class='title'>
				<view class='left'>会员专属</view>
				<view class='right'>创业礼包</view>
			</view>
			<view class='list'>
				<view class='item' v-for="(item,i) in list.data" :key="i">
					<view class='img'>
						<image src='@/static/images/imgg.png' />
					</view>
					<view class='title'>{{item.title}}</view>
					<view class="price">
						<text> <text class="rmb">¥</text>89</text>
						<view class='vip'><image src='@/static/images/index/vipBg.png' />
							<view >
								<text >会员</text>
								<text >立减0.3元</text>
							</view>
						</view>
					</view>
					<view class='priceNum'>
						<view class='price'>¥12</view>
						<view class='num'>122已售卖</view>
					</view>
				</view>
			</view>
		</view> -->
	
		<view class='btns'>
			<view class='left' @tap = "back">返回首页</view>
			<view class='right' @tap = "goVip">立即开通</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user:'',
				detail:'',
				
				active:0,
				activeData:'',
				list:[],
			}
		},
		onLoad() {
			var _this = this;
			_this.$http('user.vipList',{}).then(res=>{
				_this.detail = res.data
				_this.activeData = res.data[0]
				_this.active = 0
			})
			_this.$http('user.info',{}).then(res=>{
				_this.user = res.data
			})
			const {query} = wx.getLaunchOptionsSync()
			var a = decodeURIComponent(query.scene)
			var user = wx.getStorageSync('userInfo')
			var obj = {}
			a = a.split(",")
			if(a.length>0){
				for(let i=0;i<a.length;i++){
					a[i] = a[i].split("=")
					for(let j=0;j<a.length;j++){
						obj[a[i][0]] = a[i][1]
					}
				}
				if(obj.vipid){
					wx.setStorageSync('vipid',obj.vipid)
				}else{
					wx.setStorageSync('vipid',"")
				}
			}else{
				console.log("无进入参数")
			}
			console.log("扫描会员邀请二维码 > vip页面,",obj)
			
			// _this.$http('goods.scoreList',{}).then(res=>{
			// 	_this.list = res.data
			// })
		},
		methods: {
			back(){
				this.$tools.routerTo("/pages/index/index",true);
			},
			goVip(){
				uni.navigateTo({
					url:'/pages/index/vipOrder/vipOrder?id='+this.activeData.id,
				})
			},
			//选择红包
			cardTab(data,index){
				console.log(data,index)
				this.active = index;
				this.activeData = data
			},
		},
	}
</script>

<style lang="scss">
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
	    .topBanner{
	        width: 100%;
	        height: 453rpx;
	        &>image{
	            border-bottom-left-radius: 55rpx;
	            border-bottom-right-radius: 55rpx;
	        }   
	        position: relative;
	        .userCard{
	            width: 686rpx;
	            height: 333rpx;
	            position: absolute;
	            bottom: -62rpx;
	            left: 50%;
	            transform: translateX(-50%);
	            &>image{
	                border-radius: 20rpx;
	            }
	            .cardValue{
					color: #FFFFFF;
					font-size: 32rpx;
	                width: 100%;
	                height: 100%;
					padding: 0 42rpx;
					padding-top: 45rpx;
	                position: absolute;
	                top: 0;
	                left: 0;
					z-index: 3;
					.title{
						text-align: right;
						view{
							text-align: right;
							span{
								margin-top: 15rpx;
								display: inline-block;
								padding: 5rpx 40rpx;
								border: 1px solid #fff;
							}
						}
					}
					.user{
						font-size: 24rpx;
						margin-top: 20rpx;
						line-height: 55rpx;
						display: flex;
						justify-content: space-between;
						align-items: center;
						.userImg{
							width: 130rpx;
							height: 130rpx;
							border-radius: 50%;
							overflow: hidden;
						}
					}
	            }
	        }
	    }
	    .vipPayList{
	        margin-top: 114rpx;
	        padding: 0 32rpx;
	        overflow-x: scroll;
	        white-space:nowrap;
	        .item:last-child{
	            margin-right: 0 !important;
	        }
	        .item{
	            display: inline-block;
	            width: 449rpx;
	            height: 292rpx;
	            margin-right: 23rpx;
	            margin-bottom: 25rpx;
	            background: #FDEBBA;
	            border-radius: 10rpx;
	            padding: 32rpx 20rpx;
	            .v1{
	                font-size: 32rpx;
	                display: flex;
	                justify-content: space-between;
	                align-items: center;
	                .img{
	                    width: 60rpx;
	                    height: 60rpx;
	                    image{
	                        border-radius: 50%;
	                    }
	                }
	            }
	            .v2{
	                font-size: 24rpx;
	                margin-top: 20rpx;
	            }
	            .v3{
	                margin-top: 32rpx;
	                &>text:nth-child(1){
	                    font-size: 49rpx;
	                    color: #FF0000;
	                    .rmb{
	                        font-size: 32rpx;
	                        margin-right: 12rpx;
	                    }
	                    .price{
	                        color: #333333;
	                        font-size: 24rpx;
	                        margin-left: 12rpx;
	                        text-decoration: line-through
	                    }
	                }
	            }
	            .v4{
	                font-size: 24rpx;
	                display: flex;
	                justify-content: space-between;
	                align-items: center;
	                .img{
	                    width: 40rpx;
	                    height: 40rpx;
	                    image{
	                        border-radius: 50%;
	                    }
	                }
	            }
	        }
	    }
	    .code{
	        font-size: 24rpx;
	        text-align: center;
	        color: #999;
	        margin: 30rpx 0;
	        text{
	            color: #333;
	            margin-left: 20rpx;
	        }
	    }
	    .imgText{
	        padding: 40rpx 0;
	        margin-bottom: 20rpx;
			image{
				width: 100%;
			}
	        .item{
	            margin-bottom: 42rpx;
	            display: flex;
	            justify-content: center;
	            align-items: center;
	            .img{
	                width: 200rpx;
	                height: 200rpx;
	                margin-right: 62rpx;
	            }
	            .text{
	                max-width: 400rpx;
	                line-height: 80rpx;
	                &>view{
	                    overflow: hidden;
	                    text-overflow:ellipsis;
	                    white-space: nowrap;
	                }
	                .name{
	                    font-size: 32rpx;
	                    font-weight: 700;
	                }
	                .title{
	                    font-size: 28rpx;
	                }
	            }
	        }
	    }
	    .vipRed{
	        padding: 0 32rpx;
			margin-top: 39rpx;
	        .list{
	            display: flex;
	            justify-content: space-between;
	            flex-wrap: wrap;
	        }
	        .title{
	            display: inline-block;
	            height: 45rpx;
	            margin-bottom: 42rpx;
	            line-height: 45rpx;
	            position: relative;
	            .titleBg{
	                width: 100%;
	                height: 18rpx;
	                position: absolute;
	                bottom: 0;
	                border-top-left-radius: 100px;
	                border-bottom-left-radius: 100px;
	                background: linear-gradient(90deg,rgba(255,131,109,0.58) 3%, rgba(252,214,188,0.28))
	            }
	        }
	        .item{
	            width: 330rpx;
	            height: 159rpx;
	            margin-bottom: 32rpx;
	            display: flex;
	            &>view{
	                width: 50%;
	            }
	            .left{
	                background: #FFF4E0;
	                position: relative;
	                &>view{
	                    width: 100%;
	                    text-align: center;
	                    position: absolute;
	                    top: 50%;
	                    left: 50%;
	                    transform: translate(-50%,-50%);
	                    &>view:nth-child(1){
	                        color: #FF264A;
	                        font-size: 24rpx;
	                        text{
	                            font-size: 32rpx;
	                        }
	                    }
	                    &>view:nth-child(2){
	                        color: #A28A53;
	                        font-size: 22rpx;
	                    }
	                }
	            }
	            .right{
	                color: #333;
	                font-size: 28rpx;
	                line-height: 159rpx;
	                text-align: center;
	                background: #FEEBC0;
	                position: relative;
	                overflow: hidden;
	                view{
	                    color: #A28A53;
	                    width: 200rpx;
	                    height: 200rpx;
	                    border-radius: 40rpx;
	                    background: #292C28;
	                    position: absolute;
	                    top: -160rpx;
	                    right: -90rpx;
	                    text{
	                        font-size: 22rpx;
	                        display: inline-block;
	                        height: 40rpx;
	                        line-height: 40rpx;
	                        padding-left: 18rpx;
	                        position: absolute;
	                        bottom: 0;
	                        left: 0;
	                    }
	                }
	            }
	        }
	    }
	    .vipOnly{
	        padding: 0 32rpx;
	        .title{
	            line-height: 80rpx;
	            font-size: 32rpx;
	            display: flex;
	            align-items: center;
	            justify-content: space-between;
	            .left{
	                color: #333;
	            }
	            .right{
	                color: #999;
	            }
	        }
	        .list{
	            display: flex;
	            justify-content: space-between;
	            flex-wrap: wrap;
	            .item{
	                width: 330rpx;
	                padding: 12rpx;
	                margin-bottom: 32rpx;
	                background: #fff;
	                .img{
	                    width: 306rpx;
	                    height: 188rpx;
	                }
	                .vip{
	                    width: 156rpx;
	                    height: 33rpx;
	                    margin-top: 17rpx;
	                    margin-bottom: 18rpx;
	                    font-size: 20rpx;
	                    position: relative;
	                    &>view{
	                        width: 100%;
	                        height: 33rpx;
	                        line-height: 33rpx;
	                        position: absolute;
	                        top: 0;
	                        left: 0;
	                        z-index: 2;
	                        &>text:nth-child(1){
	                            display: inline-block;
	                            color: #C7A880;
	                            width: 55rpx;
	                            margin-right: 5rpx;
	                            text-align: center;
	                        }
	                        &>text:nth-child(2){
	                            color: #4E4E4E;
	                        }
	                    }
	                }
	                .price{
	                    margin-top: 16rpx;
	                    display: flex;
	                    justify-content: space-between;
	                    align-items: center;
	                    &>text:nth-child(1){
	                        font-size: 30rpx;
	                        color: #FF0000;
	                        .rmb{
	                            font-size: 24rpx;
	                        }
	                    }
	                }
	                .title{
	                    font-size: 25rpx;
	                    height: 70rpx;
	                    line-height: 35rpx;
	                    margin-top: 16rpx;
	                    display: -webkit-box;
	                    -webkit-box-orient: vertical;
	                    -webkit-line-clamp: 2;
	                    overflow: hidden;
	                }
	                .priceNum{
	                    display: flex;
	                    justify-content: space-between;
	                    align-items: center;
	                    color: #999999;
	                    font-size: 24rpx;
	                    .price{
	                        text-decoration: line-through;
	                    }
	                }
	            }
	        }
	    }
	
	
	
	
	
	
	    .btns{
			margin-top: 60rpx;
	        margin-bottom: 149rpx;
	        display: flex;
	        justify-content: space-between;
	        align-items: center;
	        .left{
	            color: #fff;
	            width: 268rpx;
	            height: 78rpx;
	            text-align: center;
	            line-height: 78rpx;
	            background: #ff9900;
	        }
	        .right{
	            color: #FEEBC0;
	            width: 482rpx;
	            height: 78rpx;
	            text-align: center;
	            line-height: 78rpx;
	            background: #272B2B;
	        }
	    }
</style>
