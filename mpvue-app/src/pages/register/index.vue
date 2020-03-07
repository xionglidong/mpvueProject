<template>
  <div class="container">
    <div class="login">
        <div class="info-container">
            <div class="info">
                <input type="text" placeholder="用户名" v-model="name">
                <span v-if="double">&nbsp;&nbsp;4-8个字符组成</span>
                <span v-else class="double">&nbsp;&nbsp;用户名已存在</span>
            </div>
            <div class="info">
                <input type="password" placeholder="密码" v-model="password">
                <span>&nbsp;&nbsp;不少于6个字符</span>
            </div>

            <div class="select">
              选择地区:
            </div>

            <div class="address">
              <picker mode="region" @change="bindRegionChange" :value="region">
                <div class="picker">
                 
                  <div>{{region[0]}}:</div>
                  <div>{{region[1]}}:</div>
                  <div>{{region[2]}}</div>
                </div>
              </picker>
            </div>
            <div class="gender">
              <span>性别:</span>
              <radio-group class="radio-group" @change="radioChange">
                <radio value='男'>男</radio>
                <radio value='女'>女</radio>
              </radio-group>
            </div>
            <button @click="upload">头像</button>
        </div>
      
        <button class="btn" @click="register" :disabled='dis'>注册</button>
      </div>
  </div>
</template>
<script>
export default {
    data() {
        return {
            name:'',
            password:'',
            dis:true,
            index:0,
            region:['湖北省','孝感市','孝南区'],
            gender:'',
            img:'',
            double:true,
            reg_name:/.{4,8}/,
            reg_password:/\w{6,}/
            
    
            
        }
    },
    onLoad(){
      this.name =''
      this.password =''
      this.gender =''
      this.img =''
      this.region =['湖北省','孝感市','孝南区']

    },
    mounted() {
      const db = wx.cloud.database({env: 'cloud-test-ggry6'})
      db.collection('user').get({
      success:res=>{
        this.index = res.data.length
    }
  })
    },
   watch: {
        name:function(){
             const db = wx.cloud.database({env: 'cloud-test-ggry6'})
              db.collection('user').where({
              name:this.name
              }).get({
              success:res=>{
              if(res.data.length>0){
                this.double = false
                this.dis = true
              }else{
                this.double =true
              }
              
              }
             })
          
               if(this.reg_name.test(this.name)&&this.reg_password.test(this.password)){
                   this.dis = false;
               }else{
                   this.dis = true;
               }
           },
        password:function(){
                  if(this.reg_name.test(this.name)&&this.reg_password.test(this.password)){
                   this.dis = false;
               }else{
                   this.dis = true;
               }
       }

       },
      
  methods: {
    radioChange(e){
     
      this.gender = e.mp.detail.value
    },
     bindRegionChange(e){
     
       this.region = e.mp.detail.value
    },
    upload(){
      this.dis = true
      wx.chooseImage({
        count: 1, // 最多可以选择的图片张数，默认9
        sizeType: ['original', 'compressed'], // original 原图，compressed 压缩图，默认二者都有
        sourceType: ['album', 'camera'], // album 从相册选图，camera 使用相机，默认二者都有
        success:res=>{
          this.img = res.tempFilePaths[0]
          } ,
        fail:res=>{
        },
        complete:res=>{
          this.dis = false
        }
      })
    },
    register(){
     wx.showLoading({
        title: '注册中'
      })
       wx.cloud.uploadFile({
            cloudPath:`${this.index+1}.png`,
            filePath:this.img,
            success:res =>{
              
              
              //this.index +=1
              },
              fail:res=>{
                
              },
              complete:res=>{
                this.img = this.img==''?"":`cloud://cloud-test-ggry6.636c-cloud-test-ggry6-1301414461/${this.index+1}.png`
                // this.name = this.name
                // this.password = this.password
                wx.cloud.callFunction({
                name:'register',
                data:{
                  name:this.name,
                  password:this.password,
                  region:this.region,
                  gender:this.gender,
                  img:this.img
                  }
                })
              wx.hideLoading({
              success:res=>{
              wx.showToast({
              title:'注册成功',
              icon:'success',
              success:res=>{
                this.img = ''
                this.index+=1
                wx.navigateBack({
                  delta: 1, 
                })
              }
          })
        }
      })
              }
              })
     


    }
  }
};
</script>

<style>
.radio-group{
  display: flex;
  justify-content: space-evenly;
  width: 50%;
}
.gender{
  display: flex;
  justify-content: space-evenly;
  width: 100%;
}
.gender span{
  color: #00bc12;
}
.select{
  width: 22%;
  align-self:flex-start;
  margin-left: 12%;
  color: #00bc12;
  
}

.picker{
  width: 340px;
  display: flex;
  justify-content: space-evenly;
}
.picker div{
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  width: 75px;
  border: 1px #00bc12 solid;
  border-radius: 10px;
  background-color: #00bc12;
  color: white;
}

.info-container{
  
  height: 60%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;

}
.info-container button{
    width: 20%;
    background-color: #00bc12;
    color: white;
    font-size: 15px;
}
.info input{
  border: 1px #00bc12 solid;
  border-radius: 10px;
  height:40px;
  width: 70%
}
.info{
    display: flex;
}
.info span{
    font-size: 12px;
    line-height: 40px;
}
.double{
  font-size: 12px;
  line-height: 40px;
  color: red;
}
.address{
    display: flex;
    justify-content: space-evenly;
}
.address input{
    border: 1px #00bc12 solid;
    border-radius: 10px;
    height:40px;
    width: 12%
}
.btn-container{
    height: 40%;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly
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

</style>