<template>
    <el-header class="filter-box">
      <div v-if="query.hasOwnProperty('xiaohanbao')">
        <div class="label">兄弟们你看我这汉堡做的行不行！</div>
        <div class="item">
          <el-input v-model="query.xiaohanbao" placeholder="彳亍！"></el-input>
        </div>
      </div>
      <div v-if="query.hasOwnProperty('cheshuo')">
        <div class="label">虽然不是同一个时间</div>
        <div class="item">
          <el-input v-model="query.cheshuo" placeholder="但是同一个撤硕"></el-input>
        </div>
      </div>
      <div v-if="query.hasOwnProperty('delicious')">
        <div class="label">美食届里我老八</div>
        <div class="item">
          <el-input v-model="query.delicious" placeholder="万人称我美食家！"></el-input>
        </div>
      </div>
      <div v-if="query.hasOwnProperty('zmchi')">
        <div class="label">这个要怎么吃呢？</div>
        <div class="item">
          <el-input v-model="query.zmchi" placeholder="先剥皮啊先剥皮"></el-input>
        </div>
      </div>
      <div v-if="query.hasOwnProperty('srceam')">
        <div class="label">先尝尝啊</div>
        <div class="item">
          <el-input v-model="query.srceam" placeholder="啊还没吃呢"></el-input>
        </div>
      </div>
    </el-header>
</template>

<script>
 export default {
   components: {

   },
   props: {
     querydata:{
       type:Object
     }
   },
   data() {
     return{
       query:{}
     }
   },
   watch:{
     querydata:{
       handler(v){
         this.query = v
       },
       immediate:true,
       deep:true
     },
     query:{
       handler(v){
        this.$emit('update:querydata', v)
       },
       deep:true
     }
   }
 }
</script>
<style>
.el-header {
  
  height: auto!important;
  display: flex;
  flex-wrap: wrap;
  background-color: #fff;
  padding-bottom: 10px;
}
.el-header > div {
    display: flex;
    align-items: center;
    margin: 10px 20px 0 0;
    width: 600px;
}
  .time-range {
    width: 450px;
  }
  .radio-box {
    width: 200px;
  }
</style>
