<template>
	<view>
		<view class="content">
			<map class="map" id="map" :latitude="latitude" :longitude="longitude"  scale="18" show-location="true" :markers="markers"
			 @markertap="markertap">
			 </map>
		</view>
	</view>
</template>

<script>
import amap from '../amap-wx.js';
export default {
	data() {
		return {
			amapPlugin: null,
			key: '4fd585288a5bf42c842ac9b03117252f',//高德地图key
			markers: [],
			poisdatas: [],
			latitude: 35.761293,
			longitude: 114.288087,
		}
	},
	onLoad() {
		var that = this;
		this.amapPlugin = new amap.AMapWX({
			key: this.key
		});
		this.amapPlugin.getPoiAround({
			iconPathSelected: '', //如：..­/..­/img/marker_checked.png
			iconPath: '', //如：..­/..­/img/marker.png
			success: function(data) {
				//成功回调
				that.markers = data.markers;
				that.latitude = that.markers[0].latitude;
				that.longitude = that.markers[0].longitude;
				// that.showMarkerInfo(that.markers,0);
				that.poisdatas = data.poisData;
				var markers_new = [];
				that.markers.forEach(function(item, index) {
					markers_new.push({
						id: item.id, //marker 序号
						width: item.width, //marker 宽度
						height: item.height, //marker 高度
						iconPath: item.iconPath, //marker 图标路径
						latitude: item.latitude, //marker  纬度
						longitude: item.longitude, //marker 经度
						address: item.address,//marker 地址
						name: item.name,//marker 名字
						// title:item.name,//标注点名
						joinCluster:true,
						distance: that.poisdatas[index].distance,//marker 距离中心点之间的距离 mark属性中并没有这个字段，我是因为项目需求需要知道标点跟中心点之间的距离，所以塞了一个字段进去。
						//自定义标记点上方的气泡窗口
						callout: {
							padding: 2, //callout 文本边缘留白
							fontSize: 15, //callout  文字大小
							bgColor: 'blue', //callout 背景颜色
							color: '#6B8E23', //callout 文字颜色
							borderRadius: 5, //边框圆角
							display: 'BYCLICK', //callout 'BYCLICK':点击显示; 'ALWAYS':常显
							content: that.poisdatas[index].name //地理位置名称
						}
					})
				})
				that.markers = markers_new;
			},
			fail: function(info) {
				//失败回调
				console.log("info", info)
			}
		})
	},
	methods: {
		jump(path, query) {
			this.$Router.push({
				path: path,
				query: {item:JSON.stringify(query)}
			});
		},
		//点击地图标点时触发
		markertap: function(e) {
			var id = e.markerId;
			var that = this;
			that.changeMarkerColor(that.markers,id);
		},
		changeMarkerColor: function(data, i) {
			var that = this;
			var markers = [];
			for (var j = 0; j < data.length; j++) {
				if (j == i) {
					data[j].iconPath = ""; //如：..­/..­/img/marker_checked.png
				} else {
					data[j].iconPath = ""; //如：..­/..­/img/marker.png
				}
				markers.push(data[j]);
			}
			that.marker = markers;
		}
	}
}

</script>

<style lang="less">
	.map{
		width: 100%;
		height: 100vh;
	}
	
</style>