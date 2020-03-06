<template>
  <div class="lessonDetail">
    <div class="lesson_head">
      <video
        id="myvideo"
        danmu-btn
        enable-danmu
        :danmu-list=danmulist
        @timeupdate="timeupdate"
        @ended="playend"
        :src="videoUrl"
        controls
      ></video>
      <div class="danmu">
        <input @blur="blur()" @focus='focus()' :placeholder="placeholder" v-model="danmulist"/>
        <button @click="send" @mouseup="mouseup">发送弹幕</button>
      </div>
    </div>
    <div class="lesson_content">
      <div
        @click="handleLesson(item,index)"
        class="catalogue_wrap"
        v-for="(item,index) in catalogue"
        :key="index"
      >
        <span v-if="currentIndex == index" class="active_icon"></span>
        <h4>{{item.name}}</h4>
        <img  src="/static/imgs/icon_r.jpg" alt>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  components: {
  },
  data() {
    return {
      placeholder:"请注意弹幕礼仪",
      danmulist:"",
      lessonDetail: {},
      videoUrl: "",
      currentIndex: 0,
      duration: 0,
      db:'',
      db2:'',
      option:{},
      catalogue:[]
    };
  },
  onLoad(option){
    option.db = Number(option.db)
    if(option.db2!=undefined){
    option.db2 = Number(option.db2)
    this.db2 = option.db2
    }
    this.db = Number(option.db)
    this.option = option
    this.currentIndex = 0
  },
  mounted() {
    if(this.option.db2!=undefined){
      const db = wx.cloud.database({env: 'cloud-test-ggry6'})
      db.collection('allLessons').get({
      success:res=>{
        this.db2 = this.option.db2
        this.catalogue = res.data[0].allLessons[this.db][this.db2]
        this.lessonDetail=res.data[0].allLessons[this.db][this.db2];
        this.videoUrl = res.data[0].allLessons[this.db][this.db2][0].url;
      },
      fail: res=>{
       
      }
      })}
        else{
        const db = wx.cloud.database({env: 'cloud-test-ggry6'})
        db.collection('lessondetail').get({
        success:res=>{
        this.lessonDetail=res.data[this.db].allLesson;
        this.catalogue = res.data[this.db].allLesson.catalogue
        this.videoUrl = res.data[this.db].allLesson.catalogue[0].url;
      
    }
  })  
    }
},
  methods: {
      getdanmu(){
        this.videoContext = wx.createVideoContext('myvideo')
          this.videoContext.sendDanmu({
          text: this.danmulist,
          color:'#000000'
         });
      },
      zero(){
        this.danmulist = ""
      },
      mouseup(){
        this.danmulist=""
      },
      focus(){
        this.placeholder = ""
      },
      blur(){
        this.placeholder = '请注意弹幕礼仪'
      },
      send(){
          this.videoContext = wx.createVideoContext('myvideo')
          this.videoContext.sendDanmu({
          text: this.danmulist,
          color:'#000000'
         });     
      },
    playend() {
      this.setLearnTime();
    },
    handleLesson(item, index) {
        //if(!this.lock[index]){
        this.videoUrl = item.url;
        this.currentIndex = index;
        //}
        },
    
    timeupdate(e) {
      // console.log(event);
      // 学习时长  分钟
      this.duration = Math.floor(e.mp.detail.duration / 60);
    },
    setLearnTime() {
      // 计算今天学习的总时长 = 当前视频时长 + 之前学习时长
      const time =
        this.duration + Number(wx.getStorageSync("learnInfo").minutes);
      wx.setStorage({
        key: "learnInfo",
        data: {
          minutes: time,
          percentage: Math.floor(time / 60 * 100) + "%"
        }
      });
    }
  }
};
</script>

<style>

.danmu{
  display: flex;
  width: 100%;
  height: 100%;
}
.danmu input{
  width: 65%;
  border: 1px #758a99 solid;
  border-radius: 10px;
  margin-left: 5%;
  height: 20%;

}
.danmu button{
  font-size: 64%;
  width: 22%;
  height: 19%;
  border: 1px black solid;
}
.lesson_head {
  position: relative;
  width: 100%;
  height: 200px;
  box-sizing: border-box;
}
video {
  width: 100%;
  height: 100%;
}
.lesson_content {
  margin-top: 13%;
  background-color: #fff;
  padding: 16px;
  box-sizing: border-box;
}
.catalogue_wrap {
  margin-bottom: 16px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  border: 1px solid #ebeef5;
  padding: 16px;
  border-radius: 5px;
  height: 60px;
  display: flex;
  align-items: center;
  position: relative;
}
.catalogue_wrap h4 {
  width: 95%;
  font-size: 15px;
  font-weight: bold;
}
.catalogue_wrap img {
  width: 20px;
  height: 20px;
}
.level {
  margin-right: 10px;
}
.active_icon {
  position: absolute;
  width: 3px;
  height: 80%;
  background-color: #009eef;
  left: 0;
  top: 10%;
}
</style>