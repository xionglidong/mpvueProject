<template>
  <div class="order_detail" >
    <span>{{title}}</span>
    <span class="r_icon">
      <span v-if="text" class="detail">{{text}}</span>
      
    </span>
  </div>
</template>
<script>
export default {
  props: {
    icon: String,
    title: String,
    text: String,
    erweima:String
  }
};
</script>
<style>
.order_detail {
  
  padding: 20px 0 10px 16px;
  box-sizing: border-box;
  border-bottom: 1px solid #ebeef5;
  background-color: #fff;
  overflow: hidden;
  font-size: 15px;
}

.order_detail .detail {
  font-size: 12px;
  color: #8fc31f;
  padding: 0 5px;
  background-color: #eee;
  border-radius: 20px;
  margin-right: 10px;
}
.r_icon {
  font-size: 16px;
  color: #aaa;
  float: right;
  font-weight: bold;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

</style>