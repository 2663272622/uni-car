<template>
  <view class="c-#F1F1F1">
    <view class="h-428rpx ">
      <up-image :show-loading="true" src="https://img-ischool.oss-cn-beijing.aliyuncs.com/car/base/2.png"
        width="750rpx" height="428rpx" bg-color="#0000" />
    </view>
    <view class="bg-white relative z-1 top--64rpx rounded-tl-40rpx">
      <view class="pt-52rpx ml-30rpx text-black text-opacity-90 leading-32rpx font-semibold text-36rpx">
        {{merchants.merchantName}}
      </view>
<!--      <view class="mt-24rpx flex ml-30rpx">
        <up-rate v-model="merchants.star" readonly allowHalf="true" active-color="#F25730" gutter="2rpx"></up-rate>
        <text class="font-semibold text-26rpx text-#F25730 leading-40rpx ml-6rpx">{{merchants.star}}</text>
      </view> -->
      <view class="mt-16rpx ml-30rpx flex whitespace-nowrap overflow-x-auto">
        <view v-for="(item,index) in merchants.storeLogoUrl.split(',')" :key="index" class="mr-20rpx">
          <up-image :show-loading="true" :src="item" width="216rpx" height="150rpx" bg-color="#0000"/>
        </view>
      </view>
      <view class="flex justify-between items-center mt-32rpx">
        <view>
          <view class="flex font-semibold text-26rpx leading-40rpx items-center ml-30rpx">
<!--            <view>
              <view v-if='Time' class="text-#0B86FA">营业中</view>
              <view v-else class="text-black text-opacity-60">休息中</view>
            </view> -->
            <view class="text-black text-opacity-90 mx-12rpx">{{merchants.day}}</view>
            <view class="text-black text-opacity-90">{{merchants.time}}</view>
            <up-icon name="arrow-right" size="26rpx" color="black" class="ml-18rpx"></up-icon>
          </view>
          <view class="flex u-flex-wrap">
            <view v-for="(item,index) in merchants.tag.split(',')" :key="index" class=" mt-16rpx ml-16rpx whitespace-nowrap">
              <up-tag type="info"  :text="item" color="rgba(0,0,0,0.6)" bgColor="#F0F0F0"></up-tag>
            </view>
          </view>
        </view>
        <view class="mr-38rpx ml-30rpx">
          <up-icon name="phone" size="40rpx"></up-icon>
          <text class="text-black text-opacity-60 text-20rpx leading-52rpx font-medium">电话</text>
        </view>
      </view>
      <view class="flex mt-48rpx justify-between items-center">
        <view>
          <view class="flex items-center ml-30rpx">
            <text class="font-semibold text-26rpx text-black text-opacity-90 leading-40rpx">{{merchants.address}}</text>
            <up-icon name="arrow-right" size="26rpx" class="ml-18rpx"></up-icon>
          </view>
          <view class="flex mt-16rpx ml-32rpx items-center mb-22rpx">
            <up-icon name="map" size="22rpx" class="mr-8rpx"></up-icon>
            <text class="font-medium text-24rpx text-black text-opacity-60 leading-40rpx">距您 20.0公里 驾车 50分钟 公交 56分钟</text>
          </view>
        </view>
        <view class="mr-38rpx">
          <up-icon name="map" size="40rpx"></up-icon>
          <text class="text-black text-opacity-60 text-20rpx leading-52rpx font-medium">导航</text>
        </view>
      </view>
    </view>
  </view>
</template>

<script setup lang="ts">
import {reactive} from "vue"
import carMerchantsAPI from "@/api/carMerchants";
// import getTime from "@/utils/time/index"
// const queryParams = reactive({
//   pageNum :1,
//   pageSize :10,
// });
// const loading = ref(false);
// const merchants:any=ref([])
// function handleQuery() {
//   loading.value = true;
//   let params = {
//     pageNum: queryParams.pageNum,
//     pageSize: queryParams.pageSize
//   }
//   carMerchantsAPI.getPage(params).then((data:any) => {
//     data.list.map((item:any) => {
//       item.businessScope = item.businessScope.split(",")
//     })
//     merchants.value = data.list;
//     console.log(merchants)
//     // total.value = data.total;
//     loading.value = false;
//   });
// }
// // const Time=getTime(merchants.value.openTime,merchants.value.closeTime)
// onMounted(()=>{
//     handleQuery()
// })
</script>

<style lang="scss" scoped>

</style>
