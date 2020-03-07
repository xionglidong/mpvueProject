<template>
  <div class="container">
    <div class="login">
      
        <div class="info-container">
            <input type="text" placeholder="用户名" v-model.lazy="name">
            <input type="password" placeholder="密码" v-model.lazy="password">
        </div>
      
      <div class="btn-container">
        <button class="btn" @click="register">注册</button>
        <button class="btn" @click="sign">登录</button>
      </div>
      
      <div class="learn">
        <button open-type="getUserInfo" @getuserinfo="getUserInfo">微信登录</button>
      </div>
    
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name:'',
      password : ''
    }
  },
  onShow(){
    this.name = '',
    this.password = ''
  },
  methods: {
    sign(){
      
      const db = wx.cloud.database({env: 'cloud-test-ggry6'})
      db.collection('user').where({
        name:this.name
      }).get({
      success:res=>{
      if(res.data.length ==0||this.password!=res.data[0].password){
        wx.showToast({
          title: '用户名或密码错误',
          icon: 'none'
        })
      }else{
        this.$store.dispatch("setUsers",res.data[0]);
        
        wx.switchTab({
            url: "../learn/main"
          });
      }
    }
  })
    },
    register(){
      wx.navigateTo({
        url: `../register/main`,
        success: function(res){
          // success
        }
      })
    },
    
    getUserInfo(e) {
      // 判断授权是否成功
      if (e.mp.detail.userInfo) {
        // 存储到vuex
        this.$store.dispatch("setIsAuthenticated", true);
        this.$store.dispatch("setUser", e.mp.detail.userInfo);
        wx.switchTab({
            url: "../learn/main"
          });
      }
    }
  }
};
</script>

<style scoped>
.info-container{
  
  height: 30%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;

}
.info-container input{
  border: 1px #00bc12 solid;
  border-radius: 10px;
  height:40px;
  width: 80%
}

.btn-container{
  display: flex;

}
.btn{
  width: 30%;
  background-color: #00bc12;
  color: white;
}
.container {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.login {
  display:flex;
  flex-direction: column;
  justify-content: space-evenly;
  width: 100%;
  height: 100%;
  position: relative;
}
.login img {
  display: inline-block;
  width: 100%;
  height: 100%;
}
.login .learn {
  width: 100%;
}
.learn button {
  width: 32%;
  background-color: #00bc12;
  color: white;
}
</style>
