<script>
import { mapMutations, mapActions, mapState } from 'vuex';
import wechat from '@/shopro/wechat/wechat';

export default {
	globalData: {},
	onLaunch(options) {
		let that = this;
		if(uni.getStorageSync("city")){
			console.log("已授权位置信息")
		}else{
			uni.getLocation({
				success:function(res){
					console.log(res,res.latitude,res.longitude)
					wx.request({
					  url: 'https://restapi.amap.com/v3/geocode/regeo',
					  data: {
					    location:res.longitude+','+res.latitude,
					    key: '1a9cd68da9cb0a7caf1dac3028f451ad',
					  },
					  method:'GET',
					  success (res) {
						if(res.data.infocode == 10000){
							uni.setStorageSync('province',res.data.regeocode.addressComponent.province)
							uni.setStorageSync('city',res.data.regeocode.addressComponent.city)
							
							// location.reload()
							
							uni.reLaunch({
								url:that.$Route.path
							})
							
							// console.log(that.$Route,'r')
							// if(that.$Route.path == '/pages/index/index'){
							// 	uni.reLaunch({
							// 		url:that.$Route.path
							// 	})
							// }
						}
					  }
					})
				}
			})
		}
		// const {query} = wx.getLaunchOptionsSync()
		// var a = decodeURIComponent(query.scene)
		// var obj = {}
		// a = a.split(",")
		// if(a.length>0){
		// 	for(let i=0;i<a.length;i++){
		// 		a[i] = a[i].split("=")
		// 		for(let j=0;j<a.length;j++){
		// 			obj[a[i][0]] = a[i][1]
		// 		}
		// 	}
		// 	// console.log(a,obj)
		// 	var user = uni.getStorageSync("userInfo")
		// 	if(obj.uid){
		// 		if(user){
		// 			var createDate = new Date(user.createtime*1000);
		// 			var nowDate = new Date();
		// 			// 格式化日期
		// 			var createTime = createDate.toLocaleString();
		// 			var nowTime = nowDate.toLocaleString();
		// 			console.log(createTime.split(" ")[0] , nowTime.split(" ")[0])
		// 			if(createTime.split(" ")[0] == nowTime.split(" ")[0]){
		// 				console.log("今天注册")
		// 				that.$http('user.newUser',{pid:obj.uid}).then(res=>{
							
		// 				})
		// 			}else{
		// 				console.log("注册时间: " + createTime.split(" ")[0])
		// 			}
		// 		}
		// 		uni.setStorageSync('pid',obj.uid)
		// 	}else{
		// 		uni.setStorageSync('pid',"")
		// 	}
		// 	if(obj.vipid){
		// 		uni.setStorageSync('vipid',obj.vipid)
		// 	}else{
		// 		uni.setStorageSync('vipid',"")
		// 	}
		// 	if(obj.fxid){
		// 		uni.setStorageSync('fxid',obj.fxid)
		// 		if(user){
		// 			that.$http('user.buindUser',{pid:obj.fxid,uid:user.id}).then(res=>{
						
		// 			})
		// 		}
		// 	}else{
		// 		uni.setStorageSync('fxid',"")
		// 	}
		// 	if(obj.goodsId){
		// 		uni.setStorageSync('goodsId',obj.goodsId)
		// 		console.log(obj.goodsId,"跳转")
		// 		setTimeout(()=>{
		// 			uni.navigateTo({
		// 				url: "/pages/goods/detail?id=" + obj.goodsId
		// 			})
		// 		},1)
		// 	}
		// }else{
		// 	console.log("无参数")
		// }
		// #ifdef H5
		that.$platform.entry();
		// #endif
		// #ifdef MP-WEIXIN
		// 检测小程序更新(如果从朋友圈场景进入则无此API)
		options.scene !== 1154 && wechat.checkMiniProgramUpdate();
		// #endif
		that.appInit(options);
		if (process.env.NODE_ENV === 'development') {
			this.syncPages();
		}
	},
	onShow(query) {
		let that = this;
		const scene = decodeURIComponent(query.scene);
		var a = decodeURIComponent(scene)
		var obj = {}
		a = a.split(",")
		if(a.length>0){
			for(let i=0;i<a.length;i++){
				a[i] = a[i].split("=")
				for(let j=0;j<a.length;j++){
					obj[a[i][0]] = a[i][1]
				}
			}
		}
		console.log("show",obj)
	},
	onHide() {
		console.log("关闭")
	},
	methods: {
		// 判断日期是不是今天、昨天、明天
		isToday(str){
		    let d = new Date(str).setHours(0, 0, 0, 0);
		    let today = new Date().setHours(0, 0, 0, 0);
		
		    let obj = {
		      '-86400000': '昨天',
		      0: '今天',
		      86400000: '明天',
		    };
		
		    return obj[d - today] || '啥也不是';
		}, 
		//应用初始化,获取模板数据，自动同步新页面到后台,获取用户信息
		...mapActions(['appInit', 'syncPages'])
	}
};
</script>

<style lang="scss">
@import 'uview-ui/index.scss';
@import 'static/font/shopro-icon.css';

/* 字体文件 */
// @font-face {
// 	font-family: OPPOSANS;
// 	src: url('~@/static/font/OPPOSANS-M-subfont.ttf');
// }
// @font-face {
// 	font-family: kaiti;
// 	src: url('~@/static/common/kaiti/SIMKAI.TTF');
// }
.font-OPPOSANS {
	font-family: OPPOSANS;
}
page {
	-webkit-overflow-scrolling: touch; // ios滑动不流畅
	height: 100%;
	background: #f6f6f6;
	width: 100%;
	font-size: 30rpx;
	// font-family: OPPOSANS;
	word-break: break-all; //英文文本不换行
	color: $u-main-color;
}
::-webkit-scrollbar {
	width: 0;
	height: 0;
	color: transparent;
	display: none;
}
</style>
