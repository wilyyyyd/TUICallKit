<template>
  <view class="container">
    <view class="trtc-calling-index">
      <view class="trtc-calling-index-search">
        <view class="search">
          <view class="input-box">
            <input
              class="input-search-user"
              :value="userIDToSearch"
              maxlength="50"
              type="text"
              v-on:input="userIDToSearchInput"
              :placeholder="'Search User ID'"
            />
          </view>
          <view class="btn-search" @click="searchUser">{{ "Search" }}</view>
        </view>
        <view class="search-selfInfo">
          <label class="search-selfInfo-label">{{ "Your ID" }}</label>
          <view class="search-selfInfo-phone">
            {{ config.userID }}
          </view>
        </view>
        <view class="search-result">
          <view v-if="invitee.userID" class="user-to-call">
            <view class="userInfo-box">
              <Avatar :imgSrc="invitee.avatar" />
              <text class="userInfo-name">{{ invitee.userID }}</text>
            </view>
            <view class="btn-userinfo-call" @click="call">{{ "Call" }}</view>
          </view>
          <view v-else>{{ "User not found" }}</view>
        </view>
        <view v-if="!invitee.userID" class="search-default">
          <view class="search-default-box">
            <image
              class="search-default-img"
              src="../../static/search.png"
              lazy-load="true"
            />
            <view class="search-default-message">
              {{ "initiated a call" }}
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
import Avatar from "../components/Avatar.vue";
export default {
  components: {
    Avatar,
  },
  data() {
    return {
      userIDToSearch: "",
      searchResultShow: false,
      invitee: {
        userID: "",
      },
      config: {
        sdkAppID: "",
        userID: "",
        userSig: "",
        type: 1, // 语音通话(callMediaType = 1)、视频通话(callMediaType = 2)
      },
    };
  },
  onLoad(option) {
    this.config = {
      sdkAppID: getApp().globalData.SDKAppID,
      userID: getApp().globalData.userID,
      userSig: getApp().globalData.userSig,
      type: Number(option.type),
    };
  },
  methods: {
    userIDToSearchInput(e) {
      this.userIDToSearch = e.detail.value;
    },
    searchUser() {
      // 去掉前后空格
      const newSearch = this.userIDToSearch.trim();
      this.userIDToSearch = newSearch;
      this.invitee = this.userIDToSearch;

      uni.$TUIKit
        .getUserProfile({
          userIDList: [this.userIDToSearch],
        })
        .then((imResponse) => {
          if (imResponse.data.length === 0) {
            uni.showToast({
              icon: "none",
              title: "Do not call local",
            });
            return;
          }
          this.invitee = {
            ...imResponse.data[0],
          };
          this.searchResultShow = true;
        });
    },
    call() {
      if (this.config.userID === this.invitee.userID) {
        uni.showToast({
          icon: "none",
          title: "Do not call local",
        });
        return;
      }
      try {
        uni.$TUICallKit.calls({
          userIDList: [this.invitee.userID],
          mediaType: this.config.type,
          // callParams: {
          // 	strRoomID: '123',
          // 	intRoomID: 222,
          // chatGroupID: '33333',
          // 	offlinePushInfo: {
          // 		title: 'uts title',
          // 		description: 'uts description',
          // 	},
          // 	userData: 'custom user data'
          // }
        });
      } catch (error) {}
    },
  },
};
</script>

<style>
.container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  /* background-image: url(https://mc.qcloudimg.com/static/img/7da57e0050d308e2e1b1e31afbc42929/bg.png); */
  margin: 0;
}

.trtc-calling-index {
  width: 100vw;
  height: 100vh;
  color: white;
  display: flex;
  flex-direction: column;
}

.trtc-calling-index-title {
  position: relative;
  display: flex;
  width: 100%;
  margin-top: 3.8vh;
  justify-content: center;
}

.trtc-calling-index-title .title {
  margin: 0;
  font-family: PingFangSC-Regular;
  font-size: 16px;
  color: #000000;
  letter-spacing: 0;
  line-height: 36px;
  padding: 1.2vh;
}

.btn-goback {
  position: absolute;
  left: 2vw;
  top: 1.2vh;
  width: 8vw;
  height: 8vw;
  z-index: 9;
}

.trtc-calling-index-search {
  flex: 1;
  margin: 0;
  display: flex;
  flex-direction: column;
}

.trtc-calling-index-search > .search {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-sizing: border-box;
  padding: 16px;
}

.btn-search {
  text-align: center;
  width: 60px;
  height: 40px;
  line-height: 40px;
  background: #0a6cff;
  border-radius: 20px;
}

.search-result {
  width: 90%;
  height: 40px;
  margin-left: 5%;
}

.input-box {
  flex: 1;
  box-sizing: border-box;
  margin-right: 20px;
  height: 40px;
  background: #f4f5f9;
  color: #666666;
  border-radius: 20px;
  padding: 9px 16px;
  display: flex;
  align-items: center;
}

.icon-right {
  width: 8px;
  height: 12px;
  margin: 0 4px;
}

.input-search-user {
  flex: 1;
  box-sizing: border-box;
}

.input-label {
  display: flex;
  align-items: center;
}

.input-label-plus {
  padding-bottom: 3px;
}

.input-search-user[placeholder] {
  font-family: PingFangSC-Regular;
  font-size: 16px;
  color: #8a898e;
  letter-spacing: 0;
  font-weight: 400;
}

.user-to-call {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  height: 50px;
  margin: 0;
  padding: 16px 0;
}

.userInfo-box {
  display: flex;
  align-items: center;
  font-size: 12px;
  color: #333333;
  letter-spacing: 0;
  font-weight: 500;
}

.userInfo-box > .userInfo-name {
  padding-left: 8px;
}

.btn-userinfo-call {
  width: 60px;
  height: 40px;
  text-align: center;
  background: #0a6cff;
  border-radius: 20px;
  line-height: 40px;
  margin: 10px 0;
  color: rgba(255, 255, 255);
}

.user-to-call > image {
  height: 50px;
  line-height: 50px;
  border-radius: 50px;
}

/* .title-number {
	height: 20px;
	margin-top: 3%;
	padding: 5px 0;
	border-top: 1px solid rgba(255, 255, 255, 0.11);
	font-size: 12px;
} */
.search-selfInfo {
  position: relative;
  display: flex;
  align-items: center;
  padding: 0 28px;
  font-family: PingFangSC-Regular;
  font-size: 14px;
  color: #333333;
  letter-spacing: 0;
  font-weight: 400;
}

.search-selfInfo::before {
  position: absolute;
  content: "";
  width: 4px;
  height: 12px;
  background: #9a9a9a;
  border: 1px solid #979797;
  border-radius: 2px;
  margin: auto 0;
  left: 16px;
  top: 0;
  bottom: 0;
}

.search-selfInfo-phone {
  padding-left: 8px;
}

.incoming-call {
  width: 100vw;
  height: 100vh;
}

.btn-operate {
  display: flex;
  justify-content: space-between;
  position: absolute;
  bottom: 5vh;
  width: 100%;
}

.call-operate {
  width: 15vw;
  height: 15vw;
  border-radius: 15vw;
  padding: 5px;
  margin: 0 15vw;
}

.tips {
  width: 100%;
  height: 40px;
  line-height: 40px;
  text-align: center;
  font-size: 20px;
  color: #333333;
  letter-spacing: 0;
  font-weight: 500;
}

.tips-subtitle {
  height: 20px;
  font-family: PingFangSC-Regular;
  font-size: 14px;
  color: #97989c;
  letter-spacing: 0;
  font-weight: 400;
  text-align: center;
}

.search-default {
  flex: 1;
  display: flex;
  align-items: flex-end;
  justify-content: center;
}

.search-default-box {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding-bottom: 50vh;
}

.search-default-img {
  width: 64px;
  height: 66px;
}

.search-default-message {
  width: 126px;
  padding: 16px;
  font-family: PingFangSC-Regular;
  font-size: 14px;
  color: #8a898e;
  letter-spacing: 0;
  text-align: center;
  font-weight: 400;
}
</style>
