<template>
  <div class="mine">
    <div class="user_info">
      <!-- 用户信息 -->
      <img v-if="user" :src="user.avatarUrl" alt>
      <img v-else-if="users.img==''" src="/static/imgs/null.png" alt="" >
      <img v-else :src="users.img" alt="" >
      <div class="user_detail">
        <h4 v-if="user">{{user.nickName}}</h4>
        <h4 v-else>{{users.name}}</h4>
        <span v-if="user">{{user.gender == 1 ? '男' : "女"}}</span>
        <span v-else>{{users.gender}}</span>
      </div>
    </div>
    
    <div class="order_info">
      <orderCell v-if="user" title="用户所在国家" :text="user.country"></orderCell>
      <orderCell v-else title="用户所在省份" :text="users.region[0]"></orderCell>
      <orderCell v-if="user" title="用户所在省份" :text="user.province"></orderCell>
      <orderCell v-else title="用户所在城市" :text="users.region[1]"></orderCell>
      <orderCell v-if="user" title="用户所在城市" :text="user.city"></orderCell>
      <orderCell v-else title="用户所在区县" :text="users.region[2]"></orderCell>
    </div>
    <div class="order_info mini">
      <orderCell  title="小程序二维码"></orderCell>
    </div>
    <img class="erweima" src="/static/imgs/erweima.jpg" alt="">
  </div>
</template>
<script>
import orderCell from "../../components/orderCell/index";
export default {
  data() {
    return {
    };
  },
  computed: {
    users() {
      return this.$store.getters.users;
    },
    user() {
      return this.$store.getters.user;
    },
    lessonInfo() {
      return this.$store.getters.lessonInfo;
    }
  },
  components: {
    orderCell
  },
  onShow() {
  }
};
</script>
<style>
.user_info {
  background-color: #fff;
  padding: 8px 16px;
  box-sizing: border-box;
  border-bottom: 1px solid #ebeef5;
  border-top: 1px solid #ebeef5;
  display: flex;
  flex-direction: row;
}
.user_info img {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  margin-right: 20px;
}
.user_detail h4 {
  margin: 10px 0;
  font-weight: bold;
}
.user_detail span {
  color: #aaa;
  font-size: 14px;
}
.mini{
  text-align: center
}
.erweima{
  height: 35%;
  width: 50%;
  margin-left: 25%;
}
.mine{
  height: 100%;
  width: 100%;
}
.order_info {
  margin-top: 16px;
}
</style>