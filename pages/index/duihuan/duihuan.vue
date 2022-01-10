<template>
	<!-- 店铺 -->
	<view class='shop titleBackground'>
	    <view class='topBanner'>
			<swiper :indicator-dots="true" circular="true" :autoplay="false" :interval="3000" :duration="1000" style="height: 420rpx;">
				<swiper-item v-if="shop.videofile">
					<view class="swiper-item">
						<video :src="shop.videofile" style="width: 100%;height: 100%;" controls autoplay="true"></video>
					</view>
				</swiper-item>
				<swiper-item v-for="(item,i) in shop.images" :key="i">
					<view class="swiper-item">
						<image :src="item" mode=""></image>
					</view>
				</swiper-item>
			</swiper>
	        <view class='borderBottom'></view>
	    </view>
	    <view class='listTab'>
	       <!-- <view class='tabClass'>
	            <van-tabs active="{{ active }}" bind:change="onChange" title-active-color="#FF264A" color="#FF264A">
	                <van-tab title="标签 1"></van-tab>
	                <van-tab title="标签 2"></van-tab>
	            </van-tabs>
	        </view> -->
			<view class="shopDetail" style="line-height: 40rpx;padding: 0 32rpx;">
				地址: {{shop.province_name}}{{shop.city_name}}{{shop.area_name}}{{shop.address}}
				<view class="">
					
				</view>
				电话: {{shop.phone}}
			</view>
	        <view class='list'>
	            <view class='item' v-for="(item,i) in shop.goods_list.data" :key="i">
	                <view class='img'><image :src='item.image' /></view>
	                <view class='title'>{{item.title}}</view>
	                <view class='priceNum'>
	                    <text> <text class="rmb">¥</text>{{item.price}}<text class="rmb">起</text></text>
	                    <!-- <view class='vip'><image src='@/static/images/index/vipBg.png' />
	                       <view >
	                            <text >会员</text>
	                            <text >立减0.3元</text>
	                        </view>
	                    </view> -->
	                </view>
	                <view class='priceNum'>
	                    <text style="font-size: 24rpx;color: #999999;text-decoration:line-through">¥{{item.original_price}}</text>
	                    <text>{{item.sales}}已售</text>
	                </view>
	            </view>
	        </view>
	    </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				shop:'',
			}
		},
		onLoad(options) {
			var _this = this;
			_this.$http('user.shopList',{store_id:options.id}).then(res=>{
				_this.shop = res.data;
				uni.setNavigationBarTitle({
					title:res.data.name
				})
			})
		},
		methods: {
			
		}
	}
</script>

<style lang="scss">
	image{
		width: 100%;
		height: 100%;
	}
	.pageTop{
	  width: 100%;
	  padding-top: 20px;
	  position: fixed;
	  top: 0;
	  z-index: 12;
	}
	.pageTop .fanhui{
	  padding-top: 20px;
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
	.shop{
	    min-height: 100vh;
	    background: #fff;
	    font-size: 24rpx;
	    color: #333;
	    .fanhui{
	        .iconfont{
	            color: #fff;
	        }
	    }
	    .topList{
	        color: #fff;
	    }
	    .topBanner{
	        width: 100%;
	        position: relative;
	        .borderBottom{
	            width: 100%;
	            height: 15rpx;
	            border-top-left-radius: 15rpx;
	            border-top-right-radius: 15rpx;
	            background: #fff;
	            position: absolute;
	            bottom: 0;
	        }
	        .shopText{
	            width: 100%;
	            padding: 0 36rpx;
	            position: absolute;
	            left: 50%;
	            bottom: 39rpx;
	            transform: translateX(-50%);
	            .shopTitle{
	                margin-bottom: 30rpx;
	                display: flex;
	                align-items: center;
	              .top{
	                  color: #FFFFFF;
	                  font-size: 42rpx;
	              }  
	            }
	        }
	    }
	    .listTab{
	        .van-tab{
	            flex: none !important;
	        }
	        .tabClass{
	            padding: 0 39rpx;
	            border-top-left-radius: 12rpx;
	            border-top-right-radius: 12rpx;
	        }
	        .list{
	            padding: 0 35rpx;
	            padding-top: 30rpx;
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
	                .title{
	                    height: 70rpx;
	                    margin: 23rpx 0;
	                    display: -webkit-box;
	                    -webkit-box-orient: vertical;
	                    -webkit-line-clamp: 2;
	                    overflow: hidden;
	                }
	                .priceNum{
	                    color: #999999;
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
	                }
	            }
	        }
	    }
	}
	.swiper-item{
		height: 420rpx;
	}
</style>
