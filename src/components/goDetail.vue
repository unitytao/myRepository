<template>
	<view @click="handelClick">
    <slot></slot>
	</view>
</template>

<script>
	export default {
        props: {
            list: Array,
            index: Number
        },
        methods:{
           // console.log(this.list)
           // console.log(this.index)
           //将数据缓存
           handelClick() {
            getApp().globalData.imgList =this.list
            getApp().globalData.imgIndex =this.index
            //再实现点击跳转页面
            uni.navigateTo({
               url:"/pages/imgDetail/imgDetail"
           })
           }
           
        }
	}
</script>

<style>
</style>
