<template>
  <view class="btn" @tap="handleReject">
    <image class="btn-img" :src="REJECT_SRC"></image>
    <text class="btn-text" v-if="isShowText">
      {{ t("reject") }}
    </text>
  </view>
</template>

<script setup lang="ts">
import { t } from "../../callServices/locales";
import REJECT_SRC from "../assets/hangup.png";

const props = defineProps({
  isShowText: {
    type: Boolean,
    default: true,
  },
});

const handleReject = () => {
  uni.$TUICallKit.reject();

  const pagesList = getCurrentPages();
  const currentPage = pagesList[pagesList.length - 1].route;
  if (
    currentPage ==
    "uni_modules/TencentCloud-Call/callkit/pages/callMain/callMain"
  ) {
    uni.navigateBack();
  }
};
</script>

<style scoped>
.btn {
  margin: 10px 20px;
}

.btn-img {
  width: 60px;
  height: 60px;
  border-radius: 140px;
}

.btn-text {
  font-size: 12px;
  color: #d5e0f2;
  font-weight: 400;
  text-align: center;
  margin-top: 10px;
}
</style>
