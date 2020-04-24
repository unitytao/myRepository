<template>
	<view>
        <!-- 用户信息开始 -->
        <view class="user_info">
            <view class="user_icon"><image :src ="imgDetail.user.avatar" mode="widthFix"></image></view>
            <view class="user_desc">
                <view class="user_name">{{imgDetail.user.name}}</view>
                <view class="user_time">{{imgDetail.cnTime}}</view>
            </view>
        </view>
        <!-- 用户信息结束 -->
        <!-- 高清大图开始 -->
        <view class="high_img">
            <swiper-action @swiperAction="handleSwiperAction">
            <image mode="widthFix" :src="imgDetail.thumb"></image>
            </swiper-action>
        </view>
        <!-- 高清大图结束 -->
        <!-- 点赞开始 -->
        <view class="user_rank">
            <view class="rank">
                <text class="iconfont icondianzan">{{imgDetail.rank}}</text>
            </view>
            <view class="user_collect">
                <text class="iconfont iconshoucang">收藏</text>
            </view>
        </view>
        <!-- 点赞结束 -->
        <!-- 专辑开始 -->
        <view class="album_wrap" v-if="album.lenght>0">
            <view class="album_title">相关</view>
            <view class="album_list">
                <view class="album_item"
                v-for="item in album"
                :key="item.id"
                >
                <view class="album_cover">
                    <image :src="item.cover"></image>
                </view>
                <view class="album_info">
                    <view class="album_info_text">专辑</view>
                    <view class="album_name">{{item.name}}</view>
                    <text class="iconfont iconiconfontjiantou4"></text>
                </view>
                </view>
            </view>
        </view>
        <!-- 专辑结束 -->
        <!-- 评论开始 -->
        <view class="comment_hot">
            <view class="comment_title" v-if="comment.lenght>0">
                <text class="iconfont iconhot1"></text>
                <text class="comment_text">最热评论</text>
            </view>
            <view class="comment_list">
                <view class="comment_item"
                v-for="item in hot"
                :key="item.id" 
                >
                <view class="comment_user">
                    <view class="user_icon">
                    <image :src="item.user.avatar" mode="widthFix"></image>
                </view>
                <view class="user_name">
                    <view class="user_nickname">{{item.user.name}}</view>
                    <view class="user_time">{{item.atime}}</view>
                </view>

                <view class="user_badge">
                    <image
                    v-for="item2 in item.user.title"
                    :key="item2.icon"
                    :src="item2.icon">
                    </image>
                </view>
                </view>

                <view class="coment_desc">
                    <view class="comment_content">
                        {{item.content}}
                    </view>
                    <view class="comment_like">
                        <text class="iconfont icondianzan">{{item.size}}</text>
                    </view>
                </view>
                </view>
            </view>
        </view>
        <!-- 评论结束 -->
        <view class="download">
            <view class="download_btn" @click="handleDownload">下载图片</view>
        </view>
	</view>
</template>

<script>
import monment from "moment"
import swiperAction from "@/components/swiperAction"
monment.locale("zh-cn")
	export default {
        components:{
            swiperAction
        },
		data() {
			return {
                imgDetail:{},
                //专辑数据
                album:[],
                //最热评论
                hot:[],
                //评论
                comment:[],
                imgIndex:0
			}
		},
		onLoad() {
                //console.log(getApp().globalData)
                const {imgIndex} =getApp().globalData;
                this.imgIndex =imgIndex
                this.getData()
		},
		methods: {
            getData(){
                const {imgList} =getApp().globalData;
                this.imgDetail =imgList[this.imgIndex]
                //console.log(this.imgDetail.newThumb)
                //XXX年的数据
                this.imgDetail.cnTime=monment(this.imgDetail.atime*1000).fromNow()
                //id
                // this.imgDetail.ID
                this.getComments(this.imgDetail.id)
            },
            getComments(id){
                this.request({
                   url:`http://service.picasso.adesk.com/v2/wallpaper/wallpaper/${id}/comment `
                   //url:`http://157.122.54.189:9088/image/v2/wallpaper/5e206ca6e7bce73981fb0752/comment `
                }).then(result=>{
                   this.album =result.data.res.album
                   this.hot =result.data.res.hot 
                   this.comment =result.data.res.comment
                  // console.log(result)  
                })
            },
            //滑动事件
            handleSwiperAction(e){
                //左滑INDEX++
                //右划INDEX--
                //判断数组越界
                const {imgList} =getApp().globalData;
                if(e.direction ==='right'&&this.imgIndex>0){
                    this.imgIndex--;
                    this.getData();
                }else if(e.direction ==='left'&&this.imgIndex<imgList.length-1){
                    //加载下一页
                    this.imgIndex++;
                    this.getData();
                }else{
                    uni.showToast({
                        title:"没有数据了",
                        icon : "none"
                    })
                }

            },
            async handleDownload(){

            await uni.showLoading({
                title:"下载中"
            })    
                //uni.dowmloadFile
                //uni.saveImageToPhotosAlbum
            const result1 = await uni.downloadFile({url:this.imgDetail.img})
            const {tempFilePath} =result1[1]

            const result2 = await uni.saveImageToPhotosAlbum({filePath:tempFilePath})
            //console.log("下载成功")
            uni.hideLoading()
            await uni.showToast({
                title:"下载成功",
                //icon:"none"
            })
		}
    }
}
</script>

<style>
.user_info{
    display: flex;
    padding: 20rpx;
}
.user_icon{
    padding: 0 20rpx;
}
.user_icon image{
    width: 88rpx;
    border-radius: 50%;
}
.user_name{
    color: black;
    font-weight: 600;
}
.user_time{
    color: #ccc;
    font-size: 24rpx;
    padding: 10rpx 0;
}
.user_rank{
    display: flex;
    height: 80rpx;
    border-bottom: 5rpx solid #eee;
}
.rank{
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
}
.user_collect{
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
}
.album_warp{
    padding: 20rpx;
}
.album_title{
    padding: 10rpx 0;
}
.album_item{
    display: flex;
    padding: 10rpx 0;
    border-bottom: 10rpx solid #eee;
}
.album_cover{
    flex: 1;
}
.album_cover image{
    width: 180rpx;
    height: 180rpx;
}
.album_info{
    flex: 3;
    padding-left: 20rpx;
    position: relative;
}
.album_info_text{
    width: 100rpx;
    height: 50rpx;
    background-color: deeppink;
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
}
.album_name{
    padding: 10rpx 0;
    color:#888
}
.album_info .iconfont{
    font-size: 40rpx;
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    right: 10%;
    color: black;
}
.comment_title{
    padding: 15rpx;
}
.comment_title .iconfont{
   color: red;
   font-size: 40rpx;
}
.comment_text{
    font-weight: 600;
    font-size: 36rpx;
    color: #666;
    margin-left: 10rpx;
}
.comment_list .comment_item {
  border-bottom: 15rpx solid #eee;
}
.comment_list .comment_item .comment_user {
  display: flex;
  padding: 20rpx 0;
}
.comment_list .comment_item .comment_user .user_icon {
  width: 15%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.comment_list .comment_item .comment_user .user_icon image {
  width: 90%;
}
.comment_list .comment_item .comment_user .user_name {
  flex: 1;
}
.comment_list .comment_item .comment_user .user_name .user_nickname {
  color: #777;
}
.comment_list .comment_item .comment_user .user_name .user_time {
  color: #ccc;
  font-size: 24rpx;
  padding: 5rpx;
}
.comment_list .comment_item .comment_user .user_badge image {
  width: 40rpx;
  height: 40rpx;
  display: inline-block;
}
.comment_list .comment_item .coment_desc {
  display: flex;
  padding: 20rpx 0;
}
.comment_list .comment_item .coment_desc .comment_content {
  flex: 1;
  padding-left: 15%;
}
.comment_list .comment_item .coment_desc .comment_like {
  text-align: right;
}
.download{
    height: 120rpx;
    display: flex;
    align-items: center;
    justify-content: center;
}
.download_btn{
    width: 90%;
    height: 85%;
    background-color: royalblue;
    color: #fff;
    font-size: 50rpx;
    font-weight: 600;
    display: flex;
    align-items: center;
    justify-content: center;
}
</style>
