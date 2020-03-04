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
      <element-search :options="queryOptions" :queryInit="queryInit" :querydata.sync="query"></element-search>
      <el-header>
        <el-button @click="fetchList()" class="searchBtn" type="primary">查询配料</el-button>
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
              fixed="left"
            ></el-table-column>
            <el-table-column
              align="center"
              v-if="item.slot"
              :label="item.label"
              :key="item.id"
              fixed="left"
            >
            <template slot-scope="{ row }">
              <div @click="switchDetail(row,item.prop)"
              >{{ row[item.slot] }}</div>
            </template>
            </el-table-column>
          </template>
        </el-table>
        <!-- 分页 -->
        <el-pagination
          @current-change="pageChangeHandler"
          layout="total,prev, next,jumper"
          :total="listCount"
          :current-page.sync="query.page"
          :page-size="query.pagesize"
        ></el-pagination>
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
      queryOptions:[],
      listCount:0,
      queryInit:{},
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
        //抓取数据
        if (v === 1) {
          this.tableConfigs = [
            { id:1,prop: "day", label: "扒鸭屁股" },
            { id:2,prop: "total", label: "晓汉堡" },
            { id:3,prop: "daoxiao_count", label: "柠檬" },
            { id:4,prop: "measure_count", label: "嘎嘣脆跌" },
            { id:5,prop: "unmeasure_count", label: "胡萝贝" }
          ];
          this.excelOptions = [
            {label:'全部',value:''},
            {label:'体温异常的人数',value:'1'},
            {label:'未测量的人数',value:'2'},
            {label:'体温过高的人数',value:'3'},
            {label:'体温过低的人数',value:'4'},
            {label:'体温正常的人数',value:'5'},
            {label:'已测量的人数',value:'6'},
          ]
          this.queryOptions = ['time_range']
          this.look_type = 1;
        } else {
          this.tableConfigs = [
            { id:1,prop: "name", label: "学生姓名" },
            { id:2,prop: "grade_class", label: "年级/班级" },
            { id:3,prop: "total_days", label: "应测量天数/天" },
            { id:4,prop: "measure_days",label:"已测量天数/天" },
            { id:5,prop: "abnormal_days",label:"异常天数/天" },
            { id:6,prop: "low_times",label:"体温过低/次" },
            { id:7,prop: "high_times",label:"体温过高/次" },
          ];
          this.excelOptions = [
            {label:'全部',value:''},
            {label:'异常体温数据',value:'1'},
            {label:'体温过高的数据',value:'2'},
            {label:'体温过低的数据',value:'3'},
            {label:'体温正常的数据',value:'4'}
          ]
          this.queryOptions = ['time_range','grade_id','class_id','student_name','temperature_status','measure_status']
        }
        this.$nextTick().then(()=>{
          this.fetchList()
          this.queryInit = {};
        })
      },
      immediate:true
    },
    look_type: {
      handler(v){
        if(v === 1){
          this.queryOptions = ['time_range']
        }else if(v === 2){
          this.queryOptions = ['time_range','grade_id']
        }else if(v === 3){
          this.queryOptions = ['time_range','grade_id','class_id']
        }
      }
    }
  },
  methods: {
    //点击跳转的方法
    switchDetail(row,prop){
      if(row[prop] === 0){
        return
      }
      this.topTab = 2
    },
    //私有获取列表的方法
    fetchList(){
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
