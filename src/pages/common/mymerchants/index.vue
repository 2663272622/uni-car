<template>
  <view class="oilcontainer bg-#fff box-border px-12rpx">
    <u-navbar title="我的店铺" :autoBack="true" :placeholder='true'>
    </u-navbar>
    <template v-if='display'>
      <up-form labelPosition="left" labelWidth='auto' :model="merchantsInfo" ref="formRef" :rules="rules">
        <up-form-item label="店铺图片" borderBottom ref="item1" prop="storeLogoUrl">
          <up-upload :fileList="fileList5" @afterRead="afterRead" @delete="deletePic" name="5" multiple
            sizeType='compressed' :maxCount="9"></up-upload>
        </up-form-item>
        <up-form-item label="商店名称:" borderBottom prop="merchantName">
          <up-input border="none" v-model="merchantsInfo.merchantName" placeholder="请点此输入商店名称"></up-input>
        </up-form-item>
        <up-form-item label="经营业务" borderBottom prop="businessScope">
          <up-checkbox-group v-model="actualBusinessScope">
            <up-checkbox v-for="item in business" :key="item.id" :label="item.label" :name="item.label"
              class="mr-10rpx"></up-checkbox>
          </up-checkbox-group>
        </up-form-item>
        <up-form-item label="联系电话:" borderBottom prop="contactPhone">
          <up-input border="none" v-model="merchantsInfo.contactPhone" type="number" placeholder="请点此输入联系电话"></up-input>
        </up-form-item>
        <up-form-item label="开门时间:" prop="openTime">
          <up-datetime-picker hasInput :show="show" mode="time" v-model="merchantsInfo.openTime"
            @click="show = true"></up-datetime-picker>
        </up-form-item>
        <up-form-item label="关门时间:" prop="closeTime">
          <up-datetime-picker hasInput :show="show" mode="time" v-model="merchantsInfo.closeTime"
            @click="show = true"></up-datetime-picker>
        </up-form-item>
        <up-form-item label="地址:" borderBottom prop="storeAddress">
          <up-input border="none" v-model="merchantsInfo.storeAddress" placeholder="请点击右侧选择地址"></up-input>
          <template #right>
            <u-button type="small" class='my-16rpx' @click="handlePOI">选择位置</u-button>
          </template>
        </up-form-item>
      </up-form>
      <u-button type="primary" class='my-16rpx' @click="changeMerchants()">提交修改</u-button>
    </template>
    <template v-else>
      <view v-if='merchantsInfo'>
          <view class="bg-white rd-20rpx mx-30rpx mt-30rpx flex whitespace-nowrap overflow-hidden"
            @click="handleChange(merchantsInfo)">
            <view class="my-30rpx ml-20rpx mr-12rpx">
              <up-image :show-loading="true" :src="merchantsInfo.storeLogoUrl" width="160rpx" height="160rpx" bg-color="#0000" />
            </view>
            <view class="mt-30rpx pr-20rpx w-478rpx">
              <view class="text-28rpx font-semibold text-black text-opacity-90 leading-40rpx overflow-hidden">
                {{merchantsInfo.merchantName}}
              </view>
              <view class="flex justify-between mt-6rpx">
                <view class="flex items-center">
                  <up-rate v-model="merchantsInfo.score" readonly allowHalf="true" active-color="#F25730"
                    gutter="2rpx"></up-rate>
                  <text class="font-semibold text-26rpx text-#F25730 leading-40rpx ml-6rpx">{{merchantsInfo.score}}</text>
                </view>
               <view class=" font-normal text-24rpx leading-52rpx">
                  浏览量:{{navigationData.view}}
                </view>
              </view>
              <view class="flex mt-16rpx justify-between text-black text-opacity-60 items-center">
                <view class="text-24rpx font-normal leading-40rpx overflow-hidden mr-12rpx">
                  {{merchantsInfo.storeAddress}}
                </view>
                <view class="leading-52rpx font-normal text-24rpx text-black text-opacity-90">
                  导航数:{{navigationData.navigation}}
                </view>
              </view>
            </view>
          </view>
      </view>
      <view v-else>
        <view title="新增商家" @click='addMerchants()'></view>
      </view>
    </template>
  </view>
</template>

<script setup lang="ts">
  import { usePermission } from '@/hooks';
  import { getToken, isLogin } from '@/utils/auth';
  import BusinessAPI from "@/api/business"
  import carMerchantsAPI from "@/api/carMerchants";
  import { useUserStore } from '@/store';
  import { useUpload, handleUrl } from "@/utils"

  import {
    ref
  } from "vue"
  const merchantsInfo : any = ref({
    storeLogoUrl: '',
    merchantName: '',
    businessScope: '',
    contactPhone: '',
    storeAddress: '',
  })
  const userStore = useUserStore();
  const show = ref(false);
  const display = ref(false)
  const openTime = ref();
  const closeTime = ref()
  const loginStatus = ref(false);
  const formRef : any = ref();
  onLoad(async (query) => {
    loginStatus.value = await usePermission();
    loginStatus.value = isLogin();
    const ttt = await getToken();
    if (!loginStatus.value) return;
    getBusinessList()
  })


  const fileList5 = ref([])
  const afterRead = ({ file }) => {
    uni.showLoading({
      title: '正在上传。。。'
    });
    let arr = file.map(item => {
      return useUpload(item.url, 'uni/unishop/',userStore.userId)
    })
    Promise.all(arr).then(res => {
      console.log('返回结果', res)
      res.filter(i => i).map(url => fileList5.value.push({ url }))
      uni.hideLoading()
      formatUrl()
    })
  }
  const deletePic = ({ index }) => { fileList5.value.splice(index, 1); formatUrl() }
  const formatUrl = () => { merchantsInfo.value.storeLogoUrl = fileList5.value.map(i => i.url).join(',') }

  //获取点击后的具体商家信息
  const actualBusinessScope : any = ref([])
  const handleChange = (data : any) => {
    display.value=true
    if (data.businessScope) {
      data.businessScope.split(",").forEach((data : any) => {
        let actualItem = business.value.find((item : { value : any; }) => item.value === data)
        if (actualItem) {
          actualBusinessScope.value.push(actualItem.label as string)
        }
      })
    }
    data.openTime = data.openTime.join(":")
    data.closeTime = data.closeTime.join(":")
    merchantsInfo.value = data
    if (data.storeLogoUrl) {
      fileList5.value = data.storeLogoUrl.split(',').map(url => { return { url } })
    }
  }

  // 查询业务类型
  const params = {
    pageNum: 1,
    pageSize: 10,
    dictCode: 'businessScope'
  }
  const business : any = ref([])

  function getBusiness() {
    BusinessAPI.getPage(params)
      .then((data) => {
        business.value = data.list
      })
  }
  getBusiness()

  //获取商家信息
  const getBusinessList = () => {
    carMerchantsAPI.get(userStore.openId).then(cdata => {
      cdata.list.map((item : any) => {
        return item.storeLogoUrl = item.storeLogoUrl ? item.storeLogoUrl.split(',')[0] : ''
      })
      merchantsInfo.value = cdata.list[0]
      console.log(merchantsInfo.value)
      navigation()
      // console.log(navigationData.value)

    })
  }
  //点击新增按钮
  const addMerchants = () => {
    display.value = true
    merchantsInfo.value = {}
  }
  //上传图片
  const image = ref([])
  const onChooseImage = async () => {
    uni.chooseMedia({
      mediaType: ['image'],
      sizeType: ['original', 'compressed'],
      camera: 'back',
      success: (res : any) => {
        //调用方法上传数据
        uni.uploadFile({
          url: '',
          filePath: res.tempFiles[0].tempFilePath,

        })
      }
    })
  }

  //选择地址
  const handlePOI = () => {
    uni.chooseLocation({
      success(res : any) {
        console.log(res)
        merchantsInfo.value.storeAddress = res.address
        merchantsInfo.value.latitude = res.latitude
        merchantsInfo.value.longitude = res.longitude
      }
    })
  }

  //校验规则
  const rules = reactive({
    merchantName: [{ required: true, message: "商店名称不能为空", trigger: "blur" }],
    storeAddress: [{ required: true, message: "地址不能为空", trigger: "blur" }],
    openTime: [{ required: true, message: "开门时间不能为空", trigger: "change" }],
    closeTime: [{ required: true, message: "关门时间不能为空", trigger: "change" }],
    contactPhone: [{ pattern: /^1[3456789]\d{9}$/, message: "手机号格式不正确", trigger: "blur" },
    { required: true, message: "联系电话不能为空", trigger: "blur" }],
  });

  //提交数据
  const changeMerchants = () => {
    formRef.value.validate().then((valid : any) => {
      //请求前对数据的操作
      merchantsInfo.value.userId = userStore.userId
      merchantsInfo.value.openTime = merchantsInfo.value?.openTime?.split(":").map((item : string | number) => parseInt((item)))
      merchantsInfo.value.closeTime = merchantsInfo.value?.closeTime?.split(":").map((item : string | number) => parseInt((item)))
      if (valid) {
        let actualValue : any[] = []
        actualBusinessScope.value.forEach((data : any) => {
          let actualItem = business.value.find((item : any) => item.label == data)
          if (actualItem) {
            actualValue.push(actualItem.value as string)
          }
        })
        merchantsInfo.value.businessScope = actualValue.toString()
        //发起请求
        if (merchantsInfo.value.id) {
          carMerchantsAPI.update(merchantsInfo.value.id, merchantsInfo.value).then(() => {
            uni.showModal({
              title: '修改成功',
              showCancel: false
            })
            display.value=false
            nextTick(() => {
              resetData()
              getBusinessList()
            })
          })

        } else {
          //新增商家
          carMerchantsAPI.add(merchantsInfo.value).then(() => {
            uni.showModal({
              title: '新增成功',
              showCancel: false
            })
            display.value = false
          })
          nextTick(() => {
            resetData()
            getBusinessList()
          })
        }


      }
    })
  }
  const resetData = () => {
    actualBusinessScope.value = []
    merchantsInfo.value = {
      storeLogoUrl: '',
      merchantName: '',
      businessScope: '',
      contactPhone: '',
      openTime: '',
      closeTime: '',
      storeAddress: '',
    }
  }

  //获取调用数据展示给商家
  const navigationData=ref()
  const navigation=()=>{
    carMerchantsAPI.navigationData(merchantsInfo.value.id).then((data)=>{
      navigationData.value=data
    })
  }
</script>

<style>
</style>
