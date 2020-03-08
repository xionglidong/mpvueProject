<template>
  <div class="lesson">
    <swiper 
      :indicator-dots="indicatorDots"
      :autoplay="autoplay"
      :interval="interval"
      :duration="duration"
      :circular="circular"
      indicator-active-color="rgba(255,255,255,0.6)"
    >
      <block v-for="(item,index) in imgUrls" :key="index">
        <swiper-item @click="toSwiper(item,index)">
          <img :src="item" class="slide-image">
        </swiper-item>
      </block>
    </swiper>

    <div class="classify">
      <!-- 导航 -->
      <scroll-view class="btns_wrap" scroll-x :scroll-into-view="toChildView" scroll-with-animation>
        <span
          :class="{active: currentIndex == index}"
          class="btn_scroll"
          v-for="(item,index) in allLessons"
          :key="index"
          @click="switchItem(index)"
          :id="item.id"
        >{{item.name}}</span>
      </scroll-view>
      <!-- 内容 -->
      <swiper :style="{height: swiperHeight+'rpx'}" :current="currentIndex" @change="swiperChange">
        <block v-for="(obj,i) in allLessons" :key="i">
          <swiper-item>
            <div v-for="(item,index) in obj.lessons" :key="index">
              <lessonCell
                @click="toDetail(item,index)"
                :img="item.img"
                :title="item.title"
                :level="item.level"
                :count="item.count"
                :url="item.url"
              />
            </div>
          </swiper-item>
        </block>
      </swiper>
    </div>
  </div>
</template>
<script>
import lessonCell from "../../components/lessonCell/index";
export default {
  data() {
    return {
      imgUrls: ['/static/imgs/lesson/es6.png','/static/imgs/lesson/git.png','/static/imgs/lesson/js.png'],
      allLessons: [],
      indicatorDots: true,
      autoplay: true,
      interval: 3000,
      duration: 500,
      circular: true,
      currentIndex: 0,
      toChildView: "",
      swiperHeight:720,
      database:[]
    };
  },
  mounted() {
  const db = wx.cloud.database({env: 'cloud-test-ggry6'})
  db.collection('allLesson').get({
    success:res=>{
        this.allLessons=res.data[0].allLessons
    }
  })
},
  onLoad() {
   
  },
  components: {
    lessonCell
  },
  methods: {
    toSwiper(item,index){
      wx.navigateTo({
        url: `../watchLesson/main?db=${index+2}`
      })
    },
    toDetail(item,index){
      this.database = [this.currentIndex ,index]
      wx.navigateTo({
        url: `../watchLesson/main?db=${this.currentIndex}&db2=${index}`,
        success: function(res){
          // success
        },
      })
    },
    switchItem(index) {
      this.currentIndex = index;
      this.updateView();
    },
    updateView() {
      //console.log(this.allLessons);
      this.toChildView = this.allLessons[this.currentIndex].id;
      // 计算当前tab有多少个课程数量
      const length = this.allLessons[this.currentIndex].lessons.length;
      // 更改swiperHeight的高度
      this.swiperHeight = length * 240;
    },
    swiperChange(e) {
      //console.log(e.mp.detail.current);
      this.currentIndex = e.mp.detail.current;
      this.updateView();
    }
  }
};
</script>
<style>
::-webkit-scrollbar {
  display: none;
}

.slide-image {
  width: 100%;
  height: 100%;
}

.btns_wrap {
  background-color: #fff;
  white-space: nowrap;
  border-bottom: 1px solid #ebeef5;
}
.btn_scroll {
  display: inline-block;
  padding: 0 16px;
  height: 45px;
  line-height: 45px;
  text-align: center;
  box-sizing: border-box;
  text-align: center;
}

.active {
  color: #009eef;
}
</style>
