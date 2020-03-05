<template>
  <div id="app">
    <el-container>
      <el-radio-group v-model="topTab">
        <el-radio-button :label="1">老八</el-radio-button>
        <el-radio-button :label="2">郭老师</el-radio-button>
      </el-radio-group>
      <el-header class="look_type_class" v-if="topTab === 1">
        <el-radio v-model="look_type" :label="1" border >臭豆腐</el-radio>
        <el-radio v-model="look_type" :label="2" border >俘虏</el-radio>
        <el-radio v-model="look_type" :label="3" border >老干妈</el-radio>
      </el-header>
      <element-search :querydata.sync="query"></element-search>
      <el-header>
        <el-button @click="queryStm()" class="searchBtn" type="primary">查询配料</el-button>
      </el-header>
      <el-main>
        <el-table :data="dataList" v-loading="tableLoading" border :key="topTab">
          <el-table-column type="index" label="序号" width="50px" align="center" fixed="left"></el-table-column>
          <template v-for="item in tableConfigs">
            <el-table-column
              align="center"
              v-if="!item.slot"
              :key="item.id"
              :label="item.label"
              :prop="item.prop"
            ></el-table-column>
            <el-table-column
              align="center"
              v-if="item.slot"
              :label="item.label"
              :key="item.id"
            >
            <template slot-scope="{ row }">
              <el-button @click="showAnswer(row.total)" v-if="item.slot === 'carrot'" type="primary"
              >一日三餐没烦恼，就吃什么？</el-button>
              <el-button @click="switchWithPara()" v-if="item.slot === 'zhibo'" type="primary"
              >兄弟们把彳亍！打在老八的搜索框里！</el-button>
            </template>
            </el-table-column>
          </template>
        </el-table>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import elementSearch from './components/elementSearch.vue'

export default {
  name: 'App',
  components: {
    elementSearch
  },
  data() {
    return {
      //控制顶部tab
      topTab: 1,
      //查询组
      query:{},
      look_type: 1,
      listCount:0,
      //表格
      tableLoading: false,
      dataList: [],
      tableConfigs: [],
    };
  },
  watch: {
    //左上角tab
    topTab: {
      handler(v) {
        //获取数据
        if (v === 1) {
          this.tableConfigs = [
            { id:1,prop: "day", label: "扒鸭屁股" },
            { id:2,prop: "total", label: "晓汉堡" },
            { id:3,prop: "daoxiao_count", label: "柠檬" },
            { id:4,prop: "measure_count", label: "嘎嘣脆跌" },
            { id:5,prop: "unmeasure_count", label: "问题" ,slot:'carrot'}
          ];
          const {xiaohanbao} = this.query
          this.look_type = 1;
          this.query = {xiaohanbao}
        } else {
          this.tableConfigs = [
            { id:1,prop: "name", label: "迷hotel" },
            { id:2,prop: "grade_class", label: "牛奶milk" },
            { id:3,prop: "total_days", label: "喜之郎cc果冻" },
            { id:4,prop: "measure_days",label:"烤面筋~" },
            { id:6,slot: "zhibo",label:"参观一下老八的直播间" }
          ];
          this.query = {zmchi : '',srceam:''}
        }
        this.fetchList()
      },
      immediate:true
    },
    look_type: {
      handler(v){
        if(v === 1){
          this.query = {xiaohanbao : ''}
        }else if(v === 2){
          this.query = {xiaohanbao : '',cheshuo : '',}
        }else if(v === 3){
          this.query = {delicious : ''}
        }
      }
    }
  },
  methods: {
    queryStm(){
      console.log(this.query)
    },
    //点击跳转的方法
    showAnswer(a){
      console.log(a)
      this.$message.success(`就吃老八秘制晓汉堡`)
    },
    switchWithPara(){
      this.query.xiaohanbao = '彳亍！彳亍！彳亍！彳亍！'
      this.topTab = 1
    },
    //私有获取列表的方法
    fetchList(){
      if(this.topTab === 1){
        this.dataList = [
          {
            day:'3份',
            total:'5份',
            daoxiao_count:'12份',
            measure_count:'56份',
          }
        ]
      }else{
        this.dataList = [
          {
            name:'3份',
            grade_class:'5份',
            total_days:'12份',
            measure_days:'56份',
          }
        ]
      }
    },
    //分页器
    pageChangeHandler(v) {
      this.query.page = v;
      this.fetchList();
    },
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
