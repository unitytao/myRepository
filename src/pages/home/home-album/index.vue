<template>
	<scroll-view class="album_scroll_view" scroll-y @scrolltolower="handleToLower">
	<!-- 轮播图-->
	<view class="album_swiper">
		<swiper
			autoplay
			indicator-dots
			circular
		>
		<swiper-item
			v-for="item in banner"
			:key ="item.id"
		>
		<!-- SWIPER属性
		1.自动轮播	autoplay
		2.指示器	indicator-dots
		3.衔接轮播 	circular
			swiper
			默认宽度150px -->
		
		<image :src ="item.thumb"></image>
		</swiper-item>
		</swiper>
	</view>
	<!-- 轮播图结束-->
	<!-- 专辑列表-->
	<view class="album_list">
		<navigator class="album_item"
			v-for="item in album"
			:key="item.id"
			:url="`/pages/album/album?user=${item.user.name}&desc=${item.desc}&avatar=${item.user.avatar}&id=${item.id}&name=${item.name}&img=${item.cover}`"
		>
		<view class="album_img">
			<image  :src="item.cover" mode="aspectFill"></image>
		</view>
		<view class="album_info">
			<view class="album_name">{{item.name}}</view>
			<view class="album_desc">{{item.desc}}</view>
			<view class="album_btn">
				<view class="album_attention">关注</view>
			</view>
		</view>
		</navigator>
	</view>
	<!-- 专辑列表结束-->
	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				params:{
					limit:30,
					order:"new",
					skip:0
				},
				//轮播图数据
				banner:[],
				//列表数据
				album:[],
				hasMore:true
			}
		},
		onLoad() {

		},
		methods: {
			getList(){
				this.request({
					url:"http://157.122.54.189:9088/image/v1/wallpaper/album",
					data:this.params
				}).then(result =>{
					if(this.banner.length===0){
						this.banner =result.data.res.banner
					}
					if(result.data.res.album.length===0){
						this.hasMore=false
						return
					}
					this.album =[...this.album,...result.data.res.album]
					console.log(this.album)
					//	console.log(this.banner,this.album)
				})
			},
			handleToLower(){
				if(this.hasMore){
					this.params.skip+=this.params.limit;
					this.getList()
				}else{
					uni.showToast({
						title:"没有数据了",
						icon:"none"
					})
				}	
			}

		},
		mounted(){
			//修改页面标题
			uni.setNavigationBarTitle({title:"专辑"})
			this.getList()
		}
	}
</script>

<style>
.album_scroll_view{
  height: calc(100vh - 36px);
}
.album_swiper swiper {
  height: 326.1rpx;
}
.album_swiper image {				
  height: 100%;
}
.album_list{
	padding:10rpx 0;
}
.album_item {
  display: flex;
  padding: 10rpx 0;
  border-bottom:1rpx solid #ccc;
}
.album_img {		
  flex:1;
  padding:10rpx
}
.album_img image{		
  width: 200rpx;
  height: 200rpx;
}
.album_info {
  flex:2;
  padding: 0 10rpx;
  overflow: hidden;
}
.album_name {
  font-size: 30rpx;
  color: black;
  padding: 10rpx 0;
}
.album_desc {	
  font-size: 24rpx;
  padding: 10rpx;

  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
.album_btn {
  display: flex;
  padding-top: 20rpx;
  padding-right: 20rpx;
  justify-content: flex-end;
}
.album_attention {
  font-size: 32rpx;
  color:deeppink;
  border: 1rpx solid deeppink;
  padding: 10rpx;
}
</style>
