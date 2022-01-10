<!-- 首页 -->
<template>
	<view class="home-wrap u-m-b-20">
		<!-- 空白页 -->
		<u-no-network @retry="init"></u-no-network>
		<shopro-empty v-if="!hasTemplate" :image="$IMG_URL + '/imgs/empty/template_empty.png'" tipText="暂未找到模板，请前往装修~"></shopro-empty>
		<view v-else-if="isConnected && isRefresh" class="content-box">
			<!-- 导航栏 -->
			<home-head v-if="headSwiperList && headSwiperList.length" :isScorll="isScorll" borderRadius="0" :navTitle="appName" :list="headSwiperList"></home-head>
			<!-- 自定义模块 -->
			<view class="template-box">
				<block v-for="(item, index) in homeTemplate" :key="item.id">
					<!-- 轮播 -->
					<sh-banner
						 v-if="item.type === 'banner' && index !== 0"
						:Px="item.content.x"
						:Py="item.content.y"
						:borderRadius="item.content.radius"
						:height="item.content.height"
						:list="item.content.list"
					></sh-banner>
					<view class="sousuo" v-bind:style="{ top: topHeight+'px' }" v-if="item.type === 'search'"  @tap="goSearch">
						<view><u-search disabled :show-action="false" placeholder="搜索"></u-search></view>
						<view class="text">
							<!-- <aa scrollable="true" single="true" :text="homeTemplate.search_notice"></aa> -->
							<swiper class="swiper" style="height: 100%;width: 220rpx;line-height: 25px;" :circular="true" :indicator-dots="false" :vertical="true" :autoplay="true" :interval="3000">
								<swiper-item v-for="(item,i) in homeTemplate.search_notice" :key="i" style="width: 220rpx;white-space: nowrap;text-overflow: ellipsis;overflow: hidden;">
									<view class="swiper-item uni-bg-red">{{item}}</view>
								</swiper-item>
							</swiper>
						</view>
					</view>
					<view class="" style=" background: #ff9f5a;" v-if="homeTemplate.index_notice != '' && index !== 0 && item.type === 'banner'">
						<aa scrollable="true" single="true" :text="homeTemplate.index_notice"></aa>
					</view>
					<!-- 滑动宫格 -->
					<sh-grid-swiper v-if="item.type === 'menu'" :list="item.content.list" :oneRowNum="item.content.style"></sh-grid-swiper>
					<!-- 推荐商品 -->
					<sh-hot-goods v-if="item.type === 'goods-list' || item.type === 'goods-group'" :detail="item.content" :temId="item.id"></sh-hot-goods>
					<!-- 广告魔方 -->
					<sh-adv v-if="item.type === 'adv'" :detail="item.content" @onShare="onShare"></sh-adv>
					<!-- 优惠券 -->
					<sh-coupon v-if="item.type === 'coupons'" :detail="item.content"></sh-coupon>
					<!-- 秒杀-->
					<sh-seckill v-if="item.type === 'seckill'" :detail="item.content"></sh-seckill>
					<!-- 拼团 -->
					<sh-groupon v-if="item.type === 'groupon'" :detail="item.content"></sh-groupon>
					<!-- 富文本 -->
					<sh-richtext v-if="item.type === 'rich-text'" :richId="item.content.id"></sh-richtext>
					<!-- 功能列表 -->
					<sh-cell v-if="item.type === 'nav-list'" :list="item.content.list"></sh-cell>
					<!-- 九宫格列表 -->
					<sh-grid v-if="item.type === 'grid-list'" :detail="item.content.list"></sh-grid>
					<!-- 功能标题 -->
					<sh-title-card v-if="item.type === 'title-block'" :title="item.content.name" :bgImage="item.content.image" :titleColor="item.content.color"></sh-title-card>
					<!-- 钱包 -->
					<sh-wallet v-if="item.type === 'wallet-card'"></sh-wallet>
					<!-- 订单卡片 -->
					<sh-order-card v-if="item.type === 'order-card'"></sh-order-card>
					<!-- 直播 -->
					<!-- #ifdef MP-WEIXIN -->
					<sh-live v-if="item.type === 'live' && HAS_LIVE" :detail="item.content"></sh-live>
					<!-- #endif -->
				</block>
			</view>
			<!-- 分类选项卡 -->
			<sh-category-tabs
				v-if="categoryTabsData && categoryTabsData.category_arr && categoryTabsData.category_arr.length"
				:enable="enable"
				:styleType="categoryTabsData.style"
				:tabsList="categoryTabsData.category_arr"
			></sh-category-tabs>
			<!-- 登录提示 -->
			<shopro-auth-modal></shopro-auth-modal>
			<!-- 悬浮按钮 -->
			<shopro-float-btn></shopro-float-btn>
			<!-- 连续弹窗提醒 -->
			<shopro-notice-modal v-if="!showPrivacy && isLogin"></shopro-notice-modal>
			<!-- 隐私协议 -->
			<!-- #ifdef APP-PLUS -->
			<privacy-modal v-if="shopInfo && shopInfo.name" v-model="showPrivacy"></privacy-modal>
			<!-- #endif -->
			<!-- #ifdef H5 -->
			<view class="tabbar-hack" style="height: 120rpx; width:100%;"></view>
			<!-- #endif -->
		</view>
		<view class="posiiton" @click="showYes" v-bind:style="{ top: topHeight+'px' }">
			<view class="left"><image src="../../static/images/position.png" mode=""></image></view>
			<view class="center">{{address}}</view>
			<view class="right"><image src="../../static/images/xia.png" mode=""></image></view>
		</view>
		<u-select mode="mutil-column-auto" safe-area-inset-bottom :list="addressAreaList" v-model="showSelect" @confirm="regionConfirm"></u-select>
		<tab></tab>
		<shopro-share v-model="showShare" :shareDetail="shareInfo" posterType="index"></shopro-share>
	</view>
</template>

<script>
import shBanner from './components/sh-banner.vue';
import shGridSwiper from './components/sh-grid-swiper.vue';
import shHotGoods from './components/sh-hot-goods.vue';
import shAdv from './components/sh-adv.vue';
import shCoupon from './components/sh-coupon.vue';
import shSeckill from './components/sh-seckill.vue';
import shGroupon from './components/sh-groupon.vue';
import shRichtext from './components/sh-richtext.vue';
import shCell from './components/sh-cell.vue';
import shGrid from './components/sh-grid.vue';
import shTitleCard from './components/sh-title-card.vue';
import shOrderCard from './components/sh-order-card.vue';
import shWallet from './components/sh-wallet.vue';
import shSearch from './components/sh-search.vue';
import shCategoryTabs from './components/sh-category-tabs.vue';
import tab from '../../components/shopro-tabbar/shopro-tabbar.vue'
import { mapMutations, mapActions, mapState } from 'vuex';
import share from '@/shopro/share';
import aa from '../../components/uni-notice-bar/uni-notice-bar.vue';

import privacyModal from './index/privacy-modal.vue';
import homeHead from './index/home-head.vue';

// #ifdef MP-WEIXIN
import { HAS_LIVE } from '@/env';
import shLive from './components/sh-live.vue';
// #endif

// #ifdef H5
let listenMove = document.body; //禁止手机h5下拉刷新带动整个页面。
let handle = function(e) {
	e.preventDefault();
};
// #endif

export default {
	components: {
		aa,
		tab,
		shBanner,
		shGridSwiper,
		shGroupon,
		shHotGoods,
		shAdv,
		shCoupon,
		shSeckill,
		shRichtext,
		shCell,
		shGrid,
		shTitleCard,
		shWallet,
		shOrderCard,
		shSearch,
		shCategoryTabs,

		privacyModal,
		homeHead,

		// #ifdef MP-WEIXIN
		shLive
		// #endif
	},
	data() {
		return {
			// #ifdef MP-WEIXIN
			HAS_LIVE: HAS_LIVE,
			// #endif
			isRefresh: true,
			enable: false, //是否开启吸顶。
			isConnected: true, //是否有网
			showPrivacy: false, //协议
			isScorll: false,
			showShare: false,
			tim:'',
			left:0,
			showSelect:false,//省市区弹窗
			addressAreaList: [],//省市区
			address:'',
			topHeight:'',//状态栏高度
		};
	},
	computed: {
		...mapState({
			config: ({ shopro }) => shopro.config,
			shopInfo: ({ shopro }) => shopro.config?.shop,
			isLogin: ({ user }) => user.isLogin,
			homeTemplate: ({ shopro }) => shopro.template?.home,
			hasTemplate: ({ shopro }) => shopro.hasTemplate,
		}),
		onShare() {
			this.shareInfo = share.setShareInfo();
			this.showShare = false;
		},
		// 项目标题
		appName() {
			if (this.config?.shop) {
				return this.config?.shop?.name;
			}
		},	
		// 头部模块数据
		headSwiperList() {
			if (this.homeTemplate?.length) {
				return this.homeTemplate[0]?.content?.list;
			}
		},
		//分类选项卡数据
		categoryTabsData() {
			if (this.homeTemplate?.length) {
				return this.homeTemplate[this.homeTemplate.length - 1]?.content;
			}
		}
	},
	onPullDownRefresh() {
		this.init();
	},
	onPageScroll(e) {
		this.isScorll = e.scrollTop > 100 ? true : false;
	},
	onShow() {
		// uni.removeStorageSync('topid')
		let that = this;
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
			if(obj.uid){
				wx.setStorageSync('pid',obj.uid)
				if(user){
					var createDate = new Date(user.createtime*1000);
					var nowDate = new Date();
					// 格式化日期
					var createTime = createDate.toLocaleString();
					var nowTime = nowDate.toLocaleString();
					if(createTime.split(" ")[0] == nowTime.split(" ")[0]){
						that.$http('user.newUser',{pid:obj.uid}).then(res=>{
							
						})
					}else{
						console.log("注册时间: " + createTime.split(" ")[0])
					}
					that.$http('user.buindUser',{pid:obj.uid,uid:user.id}).then(res=>{
						
					})
				}
			}else{
				wx.setStorageSync('pid','')
			}
			// if(obj.uid){
			// 	if(user){
			// 		that.$http('user.buindUser',{pid:obj.uid,uid:user.id}).then(res=>{
						
			// 		})
			// 	}
			// }
			// if(obj.goodsId){
			// 	setTimeout(()=>{
			// 		uni.navigateTo({
			// 			url: "/pages/goods/detail?id=" + obj.goodsId
			// 		})
			// 	},1)
			// }
		}else{
			console.log("无进入参数")
		}
		this.isLogin && this.getCartList();
		// #ifdef APP-VUE
		// 网络变化检测
		uni.onNetworkStatusChange(res => {
			this.isConnected = res.isConnected;
		});
		uni.getNetworkType({
			success: res => {
				res.networkType == 'none' ? (this.isConnected = false) : (this.isConnected = true);
			}
		});
		// #endif
	},
	onHide() {
		this.enable = false;
	},
	
	onLoad() {
		
		this.getArea();
		this.sdadad()
		if(uni.getStorageSync("city")){
			this.address = uni.getStorageSync("city")
		}else{
			this.address = "请选择城市"
		}
		// #ifdef APP-VUE
		// app隐私协议弹窗
		if (!plus.runtime.isAgreePrivacy()) {
			this.showPrivacy = true;
			this.showNoticeModal = false;
		}
		// #endif
	},
	methods: {
		sdadad(){
			var _this = this;
			_this.topHeight = uni.getMenuButtonBoundingClientRect().top
		},
		goSearch(){
			uni.navigateTo({
				url:'/pages/public/search'
			})
		},
		showYes(){
			this.showSelect = true
		},
		//获取省市区
		getArea() {
			this.$http('address.area').then(res => {
				if (res.code === 1) {
					let { provinceData, cityData, areaData } = res.data;
					provinceData.forEach((item, index) => {
						this.addressAreaList.push({ ...item, children: [] });
						this.addressAreaList[index].children.push(...cityData[index]);
						// this.addressAreaList[index].children.forEach((item1, index1) => {
						// 	item1['children'] = [];
						// 	item1.children.push(...areaData[index][index1]);
						// });
					});
				}
			});
		},
		//确认省市区
		regionConfirm(e) {
			uni.setStorageSync("province",e[0].label)
			uni.setStorageSync("city",e[1].label)
			// this.address = `${e[0].label}-${e[1].label}-${e[2].label}`;
			this.address = `${e[1].label}`;
			this.init()
		},
		gogo(){
			uni.navigateTo({
				url: '/pages/duihuan/duihuan',
			})
		},
		...mapActions(['appInit', 'getTemplate', 'getCartList']),
		jweixin() {
			this.$wxsdk.openLocation();
		},
		// 初始化
		init() {
			let that = this;
			this.isRefresh = false;
			return Promise.all([this.getTemplate()]).then(() => {
				uni.stopPullDownRefresh();
				this.isRefresh = true;
				if(uni.getStorageSync("city")){
					this.address = uni.getStorageSync("city")
				}else{
					uni.getLocation({
						success:function(res){
							console.log(res,res.latitude,res.longitude)
							wx.request({
							  url: 'https://restapi.amap.com/v3/geocode/regeo', //仅为示例，并非真实的接口地址
							  data: {
							    location:res.longitude+','+res.latitude,
							    key: '1a9cd68da9cb0a7caf1dac3028f451ad',
							  },
							  method:'GET',
							  success (res) {
								if(res.data.infocode == 10000){
									uni.setStorageSync('province',res.data.regeocode.addressComponent.province)
									uni.setStorageSync('city',res.data.regeocode.addressComponent.city)
									that.address = res.data.regeocode.addressComponent.city
								}
							  }
							})
						}
					})
				}
			});
		}
	}
};
</script>

<style lang="scss">
	.sousuo{
		width: 300rpx;
		position: fixed;
		left: 52%;
		transform: translateX(-50%);
		z-index: 1;
		/deep/ .uni-noticebar{
			background: #f2f2f2 !important;
		}
		/deep/ .uni-noticebar__content-text{
			color: red !important;
		}
		.text{
			color: #de8c17;
			width: 240rpx;
			height: 25px;
			padding: 0 5rpx;
			padding-top: 2px;
			position: absolute;
			top: 1px;
			left: 55rpx;
			z-index: 11;
			overflow: hidden;
			border-top-right-radius: 100px;
			border-bottom-right-radius: 100px;
			background: #f2f2f2 !important;
			.swiper-item{
				width: 220rpx !important;
				white-space: nowrap;
				text-overflow: ellipsis;
				overflow: hidden;
			}
		}
	}
	/deep/ .u-search .u-content{
		height: 30px !important;
		border: 1px solid #7d7d7d;
		border-radius: 100px;
	}
	.posiiton{
		height: 30px !important;
		display: flex;
		align-items: center;
		position: fixed;
		left: 22rpx;
		border: 1px solid #7d7d7d;
		border-radius: 100px;
		image{
			width: 100%;
			height: 100%;
		}
		.left,.right{
			width: 40rpx;
			height: 40rpx;
		}
		.center{
			margin: 0 5rpx;
		}
	}
	
</style>
