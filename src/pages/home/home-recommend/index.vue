<template>
<scroll-view @scrolltolower="handleToLower" scroll-y class="recommend_view" v-if="recommends.items.length>0">
	<!--推荐开始 -->
	<view class="recommend_warp">
	  <navigator v-for ="item in recommends.items"
		:key="item.id"
		:url="`/pages/album/album?id=${item.target}`"
		class="recommend_item">
		<image mode="widthFix" :src="item.thumb"></image>
	  </navigator>
	</view>
	<!--推荐结束 -->
	<!-- 月份 -->
	<view class="months_warp">
	<view class="months_title">
		<view class="months_title_info">
			<view class="months_info" >
				<text>{{date.day}}/</text>
				{{date.month}}月
			</view>
			<view class="months_text">{{months.title}}</view>
		</view>
		<view class="months_title_more">
			更多>
		</view>
	</view>
	</view>
	<view class="months_content">
		<view class="months_item"
		v-for="(item, index) in months.items"
		:key="item.id"
		>
		<go-detail :list="months.items" :index="index">
		<image :src="item.thumb+item.rule.replace('$<Height>',360)" mode="aspectFill"></image>
		</go-detail>
		</view>
	</view>
	<!-- 月份 -->
	<!-- 热门 -->
	<view class="hots_warp">
		<view class="hots_title">
			<text>热门</text>
		</view>
		<view class="hots_content">
		<view class="hots_item"
		v-for="(item, index) in hots"
		:key="item.id"
		>
		<go-detail :list="hots" :index="index">
		<image :src="item.thumb" mode="aspectFill"></image>
		</go-detail>
		</view>
	</view>
	</view>
	<!-- 热门 -->
</scroll-view>
</template>

<script>
import moment from "moment"
import goDetail from "@/components/goDetail"
	export default {
		data() {
			return {
				//推荐列表
				recommends:[],
				months:{},
				date:{
					month:'',
					day:''
				},
				hots:[],
				//请求参数
				params:{
					//获取条数
					limit:30,
					//关键词
					order:"hot",
					//跳过几条
					skip:0
				},
				hasMore:true
			}
		},
		methods: {
			//获取接口数据
			getList(){
				this.request({
				url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
				data: this.params,
			}).then(result=>{
				if(result.data.res.vertical.length===0){
					this.hasMore =false
					return;
				}
				if(this.recommends.length===0){
					this.recommends =result.data.res.homepage[1];
					console.log(this.recommends)
					this.months =result.data.res.homepage[2];
					this.date.month =moment(this.months.stime).format("MM")
					this.date.day=moment(this.months.stime).format("DD")
				}
				this.hots =[...this.hots, ...result.data.res.vertical]
			})
			},
			//滚动条触底事件
			handleToLower(){
				if(this.hasMore){
					this.params.skip+= this.params.limit
					this.getList()
				}else{
					uni.showToast({
						title:"没有数据了",
						icon:"none"
					})
				}
			}
		},
		mounted() {
			uni.setNavigationBarTitle({title:"推荐"})
			this.getList()
		},
		components:{
			goDetail
		}
	}
</script>

<style scoped>
.recommend_view{
	height: calc(100vh - 36px);
}
.recommend_warp{
	display: flex;
	flex-wrap: wrap;
}
.recommend_item{
	width: 48.5%;
	border:1px solid #fff;
	display:flex;
}
.months_warp{
	margin-top: 10px;
	padding: 0px;
}
.months_title{
	display: flex;
	justify-content: space-between;
}
.months_title_info{
	color:cornflowerblue;
}
.months_info{
	display: inline-block;
}
.months_text{
	margin-left: 20PX;
	color: black;
	display: inline-block;
	font-weight: 700;
}
.months_title_more{
	display: inline-block;
	color:cornflowerblue;
}
.months_content{
	display: flex;
	flex-wrap: wrap;
}
.months_item{
	width: 33.3%;
	border:1px solid #fff;
}
.hots_warp{
	margin: 0%;
}
.hots_title{
	border-left: 5px solid red;
	color: red;
	padding-left: 15px;
	font-weight: 600;
}
.hots_content{
	display: flex;
	flex-wrap: wrap;
}
.hots_item{
	width: 32.5%;
	border:1px solid white
}
</style>


