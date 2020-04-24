<template>
	<view>
        <view class="home_tab">
        <view class="home_tab_title">
        <view class="title_inner">
        <uniSegmentedControl 
        :current="current" 
        :values="items.map(v=>v.title)" 
        @clickItem="onClickItem" 
        style-type="text" 
        active-color="#1296db">
            </uniSegmentedControl>
       </view>
        <view class="iconfont iconsearch"></view>
        </view>
        <view class="home_tab_content"></view>
       
        <view class="content">
            <view v-if="current === 0">
                <homeRecommend></homeRecommend>
            </view>
            <view v-if="current === 1">
                <homeCategory></homeCategory>
            </view>
            <view v-if="current === 2">
                <homeNew></homeNew>
            </view>
            <view v-if="current === 3">
                <homeAlbum></homeAlbum>
            </view>
        </view>  
        </view>     
	</view>
</template>

<script>
import uniSegmentedControl from "@dcloudio/uni-ui/lib/uni-segmented-control/uni-segmented-control.vue"
import homeAlbum from './home-album/index'
import homeCategory from './home-category/index'
import homeNew from './home-new/index'
import homeRecommend from './home-recommend/index'
	export default {
        components:{
            homeAlbum,
            homeCategory,
            homeNew,
            homeRecommend,
            uniSegmentedControl
        },
        data() {
        return {
            items: [
                {title: "推荐"},
                {title: "分类"},
                {title: "最新"},
                {title: "专辑"}
            ],
            current: 1
        }
        },
         methods: {
             onClickItem(e) {
            if (this.current !== e.currentIndex) {
                this.current = e.currentIndex;
            }
        },
        //onLoad(){
        //    this.request({
        //       url:"http://157.122.54.189:9088/image/v3/homepage/vertical"
              //url: "http://localhost:8090/course/selectAll"
        //    }).then(res=>{
        //        console.log(res);
        //    })
        //}
    }
	}
</script>

<style scoped>
.home_tab{
   margin: 0px;
}
.title_inner{
    width: 60%;
    margin: 0 auto;
}
.iconsearch{
    position: absolute;
    top: 50%;
     transform: translateY(-50%);
     right: 5%;
}
 .home_tab_title{
        position: relative;
}        
</style>
