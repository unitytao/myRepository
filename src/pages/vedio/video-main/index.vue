<template>
  <scroll-view class="video_main"
               enable-flex
               scroll-y
               @scrolltolower="handleScrollTolower">
    <view class="video_item"
          v-for="item in videowp"
          :key="item.id"
          @click="handleGoVideo(item)">
      <image :src="item.img"
             mode="widthFix" />
    </view>
  </scroll-view>
</template>

<script>
export default {
  props: {
    urlobj: Object
  },
  data () {
    return {
      videowp: [],
      hasMore: true
    }
  },
  watch: {
    // 监听 urlobj 改变
    urlobj () {
      this.videowp = []
      this.getList()
    }
  },
  mounted () {
    // console.log(this.urlobj);
    this.getList()
  },
  methods: {
    async getList () {
      const result = await this.request({
        url: this.urlobj.url,
        data: this.urlobj.params
      });
      if (result.data.res.videowp.length === 0) {
        this.hasMore = false
        return
      }
      this.videowp = [...this.videowp, ...result.data.res.videowp]
    },
    handleScrollTolower () {
      if (this.hasMore) {
        this.urlobj.params.skip += this.urlobj.params.limit
        this.getList()
      } else {
      }
    },
    handleGoVideo (item) {
      getApp().globalData.video = item
      uni.navigateTo({
        url: '/pages/vedioPlay/index'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.video_main {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .video_item {
    width: 33.33%;
    border: 5rpx solid #fff;
  }
}
</style>