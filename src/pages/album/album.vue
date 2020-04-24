<template>
	<view>
        <view v-if="data.name!=undefined">
        <!-- 背景 -->
	<view class="album_bg">
        <image :src="data.img" mode="widthFix"></image>
        <view class="album_info">
            <view class="album_name">{{data.name}}</view>
            <view class="album_btn">关注专辑</view>
        </view>
    </view>
        <!-- 背景结束 -->

        <!-- 专辑作者 -->
        <view class="album_author">
            <view class="album_author_info">
                <image mode="widthFix" :src="data.avatar"></image>
                <view class="author_name">{{data.user}}</view>
            </view>
            <view class="album_author_desc">
                <text>
                    {{data.desc}}
                </text>
            </view>
        </view>
        <!-- 专辑作者结束 -->
        </view>
        <!-- 列表开始 -->
        <view class="album_list">
            <view class="album_item"
            v-for="(item, index) in wallpaper"
            :key="item.id"
            >
            <go-detail :list="wallpaper" :index="index">
            <image :src= "item.thumb+item.rule.replace('$<Height>',360)" mode="aspectFill"></image>
            </go-detail>
            </view>
        </view>
        <!-- 列表结束 -->
	</view>
</template>

<script>
import goDetail from "@/components/goDetail"
	export default {
        components:{
            goDetail
        },
		data() {
			return {
                params:{
                    limit:30,
                    order:"new",
                    skip:0,
                    frist:1
                },
                id:-1,
                data:{
                    // name:'',
                    // img:'',
                    // avatar:'',
                    // user:'',
                    // desc:''
                },
                wallpaper:[],
                hasMore:true
			}
		},
		methods: {
            getList(){
                this.request({
                   // url:`/dpc/image/v1/wallpaper/album/${this.id}/wallpaper`,
                    url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
                    data:this.params
                }).then(result =>{
                    console.log(result)
                    if(this.params.frist!=1){
                        console.log(result)
                        if(result.data.res.wallpaper.length===0){
                        this.hasMore=false
                        uni.showToast({
                         title:'没有更多了！',
                         icon:'none'
                         })
                        console.log(this.hasMore)
                    }
                    }
                    this.wallpaper =[...this.wallpaper,...result.data.res.wallpaper]
                    //console.log(this.wallpaper)
                })
            }
		},
		onLoad(options){
            console.log(options)
            this.id =options.id
            this.data.img =options.img
            this.data.name =options.name
            this.data.desc =options.desc
            this.data.avatar =options.avatar
            this.data.user =options.user
            this.getList()
            console.log(this.data)
        },
        onReachBottom(){
           if(this.hasMore){
               this.params.frist=0;
               this.params.skip+=this.params.limit;
               this.getList();
           }
        }
	}
</script>

<style>
.album_bg{
    position: relative;
}
.album_bg image{

}
.album_info{
    position: absolute;
    width: 100%;
    left: 0;
    bottom: 0;
    display: inline-flex;
    justify-content: space-between;
    height: 80rpx;
    align-items: center;
    color: #ffffff;
    padding: 0 15rpx;
}
.album_name{
    font-size: 40rpx;
}
.album_btn{
    background-color: deeppink;
    width:152rpx;
    height: 60rpx;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius:10rpx;
}
.album_author{
    padding: 10rpx;
}
.album_author_info{
    padding: 10rpx 0;
    display: flex;
}
.album_author_info image{
    width: 50rpx;
}
.author_name{
    color: black;
    margin-left: 15rpx;
}
.album_author_desc{

}
.album_list{
    display: flex;
    flex-wrap: wrap;
}
.album_item{
    width: 33.3%;
    border: 3rpx solid white;
}
.album_item image{
    height: 160rpx;
}
.comment_hot {

}

</style>
