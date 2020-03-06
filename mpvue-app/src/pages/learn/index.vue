<template>
  <scroll-view class="learn" scroll-y>
    <div class="learn_info">
      <img src="/static/imgs/plan.jpg" alt>
      <div class="info_text">
        <h4>
          已学习
          <span>{{minutes}}</span> 分钟
        </h4>
        <p>
          今日目标已完成
          <span>{{percentage}}</span>
        </p>
      </div>
    </div>
    <div class="hot_lesson">
      <cart-header title="今日最热课程" @click="switchToHotLessons"></cart-header>
      <div v-for="(item,index) in hotLessons" :key="index">
        <lesson-cell 
          @click="toDetail(item,index)"
          :img="item.img"
          :title="item.title"
          :level="item.level"
          :count="item.count"
          :url="item.url"
        ></lesson-cell>
      </div>
    </div>
  </scroll-view>
</template>
<script>
import cartHeader from "../../components/cartHeader/index";
import lessonCell from "../../components/lessonCell/index";
import { formatTime } from "../../utils";
export default {
  data() {
    return {
      minutes: 0,
      percentage: "0%",
      lessonCount: 0,
      hotLessons:[],
      database:['lessondetail','Angular']
                    
    };
  },
  mounted() {
  const db = wx.cloud.database({env: 'cloud-test-ggry6'})
  db.collection('hotlesson').get({
    success:res=>{
        this.hotLessons=res.data[0].hotlesson
    }
  })
},
        
  onShow() {
    
    this.setLearnInfo();
  },
  computed: {
    
  },
  components: {
    cartHeader,
    lessonCell
  },
  methods: {
    toDetail(item,index){
      wx.navigateTo({
        //url: `../watchLesson/main?db=${this.database[index]}`,
        url: `../watchLesson/main?db=${index}`,
        success: function(res){
          // success
        },
        fail: function() {
          // fail
        },
        complete: function() {
          // complete
        }
      })
    },
    switchToHotLessons() {
      wx.switchTab({
        url: "../lesson/main"
      });
    },
    setLearnInfo() {
      let self = this;
      // 获取当前日期 2020-1-10 12:00:00 new Date()
      let date = formatTime(new Date()).split(" ")[0];
      // console.log(date);
      wx.getStorage({
        key: "date",
        success(res) {
          // console.log(res);
          if (res.data != date) self.storageDate(date);
          else {
            // 显示当前数据
            const learnInfo = wx.getStorageSync("learnInfo");
            self.minutes = learnInfo.minutes;
            self.percentage = learnInfo.percentage;
          }
        },
        fail() {
          // 如果没有时间date 存储
          self.storageDate(date);
        }
      });
    },
    storageDate(date) {
      // 存储当前日期,并将数据初始化
      wx.setStorage({
        key: "date",
        data: date
      });

      wx.setStorage({
        key: "learnInfo",
        data: {
          minutes: 0,
          percentage: "0%"
        }
      });
      this.minutes = 0;
      this.percentage = "0%";
    }
  }
};
</script>

<style>
.learn {
  height: 100%;
  box-sizing: border-box;
}
.learn_info {
  padding: 10px;
  display: flex;
  flex-direction: row;
  background-color: #fff;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  border: 1px solid #ebeef5;
}
.learn_info img {
  width: 85px;
  height: 85px;
}
.info_text {
  padding: 10px;
}
.info_text h4 {
  font-weight: bold;
}
.info_text h4 span {
  color: #009eef;
}
.info_text p {
  color: #ccc;
  font-size: 14px;
  margin-top: 10px;
}
.info_text p span {
  color: #85c646;
}

.hot_lesson {
  width: 100%;
  margin-top: 16px;
  background-color: #fff;
  border: 1px solid #ebeef5;
}

::-webkit-scrollbar {
  display: none;
}
</style>