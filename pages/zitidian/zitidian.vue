<template>
	<view class="vipOpen">
		<view class="form">
			<view class="item">
				<view class="label">
					模板名称: 
				</view>
				<view class="input">
					<u-input v-model="name" placeholder="请输入模板名称" clearable="false" border="true"/>
				</view>
			</view>
			<view class="item">
				<view class="label">
					有效时间: 
				</view>
				<view class="input radio">
					<view :class="active == 0 ? 'day active' : 'day'" @click="type(0)">
						<view class="left">
							<view class="wai"><view class="nei"></view></view>
						</view>
						<view class="right">有效天数</view>
					</view>
					<view :class="active == 1 ? 'time active' : 'time'" @click="type(1)">
						<view class="left">
							<view class="wai"><view class="nei"></view></view>
						</view>
						<view class="right">截止日期</view>
					</view>
				</view>
			</view>
			<view class="item">
				<view class="label">
					<span v-if="active == 0">有效天数: </span>
					<span v-if="active == 1">截止日期</span>
				</view>
				<view class="input">
					<u-picker mode="time" v-model="show" :params="params" @confirm="queding"></u-picker>
					<u-input v-if="active == 0" type="number" v-model="time1" placeholder="请输入有效天数" clearable="false" border="true"/>
					<view v-if="active == 1" class="timePupop" @click="show=true">{{date}}</view>
				</view>
			</view>
		</view>
		<view class="btn" style="margin-top: 100rpx;">
			<u-button type="primary" class="custom-style" @click="tijiao">提交</u-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user:'',
				active:0,
				
				id:'',
				name:'',
				time1:'',
				time2:'',
				endTime:'',
				
				params: {
					year: true,
					month: true,
					day: true,
					hour: true,
					minute: true,
					second: false,
				},
				date:"请选择截止日期",
				show: false
			}
		},
		onLoad(options) {
			var _this = this
			if(options.id){
				this.id = options.id
			}
			_this.$http('user.info',{}).then(res=>{
				_this.user = res.data
			})
		},
		methods: {
			type(i){
				this.active = i
			},
			queding(e){
				this.date = e.year+"-"+e.month+"-"+e.day+" "+e.hour+":"+e.minute
			},
			tijiao(){
				var _this = this;
				
				if(!this.id || !this.name){
					uni.showToast({
						title:'请填写完整信息',
						icon: 'none',
					})
					return;
				}
				if(this.active == 0){
					if(!this.time1){
						uni.showToast({
							title:'请填写完整信息',
							icon: 'none',
						})
						return;
					}
				}else{
					if(!this.time2){
						uni.showToast({
							title:'请填写完整信息',
							icon: 'none',
						})
						return;
					}
				}
				_this.$http('user.set_selfetch',{
					store_id:this.id,
					name:this.name,
					expire_type:this.active == 0 ? "day" : "time",
					expire_day:this.time1,
					expire_time:this.time2,
				}).then(res=>{
					if(res.code == 1){
						uni.navigateBack({
							delta:1
						})
						uni.showToast({
							title:"自提点提交成功",
							icon:"none",
						})
					}
				})
			},
		}
	}
</script>

<style lang="scss">
	.custom-style{
		width: 680rpx;
		height: 80rpx;
	}
	.vipOpen{
		padding: 0 32rpx;
		padding-top: 50rpx;
		min-height: 100vh;
		background: #fff;
	}
	image{
	  width: 100%;
	  height: 100%;
	} 
	view{
		box-sizing: border-box;
	}
	.form{
		.item{
			margin-bottom: 30rpx;
			.label{
				margin-bottom: 20rpx;
			}
			.timePupop{
				height: 37px;
				border: 1px solid #dcdfe6;
				line-height: 37px;
				padding-left: 10px;
				font-size: 14px;
				color: #b5b6b7;
				border-radius: 6rpx;
			}
		}
		.radio{
			display: flex;
			align-items: center;
			.wai,.nei{
				background: #fff;
				border-radius: 50%;
				border: 1px solid #eee;
			}
			.wai{
				width: 40rpx;
				height: 40rpx;
				position: relative;
			}
			.nei{
				width: 20rpx;
				height: 20rpx;
				position: absolute;
				top: 50%;
				left: 50%;
				transform: translate(-50%,-50%);
			}
			.active .wai{
				background: #2979FF;
			}
			.day,.time{
				margin-right: 30rpx;
				display: flex;
				align-items: center;
			}
		}
	}
</style>
