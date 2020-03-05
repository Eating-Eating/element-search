# 表格+搜索栏要怎么写呢？欸还没写呢：萌新的偷懒技巧
> 大家好我是前端练习生神轻易，这篇文章记录一下工作中基于element开发的后台表格页的写法，还有封装一个搜索栏的组件

前段时间在家办公，b站一刷就是一天。转眼就十二点了，第二天一早就要发测，咋办呢？先看看需求吧！

是个后台的页面，要求顶部有个tab可以切换老八跟郭老师两种身份，底下是搜索栏+table。哦，又是个常规的表格页啊，特殊之处在于老八的搜索栏内容要根据另一个tab变化，郭老师的页面可以点击某个按钮跳转到老八那带参数搜索，下面是这个需求的动图

![](C:\Users\qians\Desktop\element-search\demand.gif)

一眼看上去，直接写两个页面把参数带到路由参数里请求就OK了，然而由于我实在太困了，不想写两个页面，更不想写这么多表格页的代码：

![](C:\Users\qians\Desktop\element-search\img\tableColumn.png)

## 进入正题一：如何优雅地写element的表格：

> ​	显而易见，在纯展示数据的列，一般参数都比较固定，就是align,prop,label，分别表示align,显示数据的key，列标题。那么我们用一个v-for指令就可以完成了。

直接上代码

```html
<el-table :data="dataList" v-loading="tableLoading" border>
	<el-table-column
     v-for="item in tableConfigs"
	 align="center"
	 :key="item.id"
	 :label="item.label"
	 :prop="item.prop"
	></el-table-column>
</el-table>
```

```javascript
data() {
    return {
       tableConfigs:{
            { id:1,prop: "day", label: "扒鸭屁股" },
            { id:2,prop: "total", label: "晓汉堡" },
            { id:3,prop: "daoxiao_count", label: "柠檬" },
            { id:4,prop: "measure_count", label: "嘎嘣脆跌" },
            { id:5,prop: "unmeasure_count", label: "问题" }
       } 
    }
}
```

搞完了发现又一个问题，第一，我打算把两个表格页放在一起，通过监听顶部tab的属性（定义为tabTop）控制不同的表格展示，第二，通常表格页都会有夹一些自定义列，显然直接用prop满足不了我们的需求，那么该如何改写呢？

```html
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
```

```javascript
data(){
    return{
        tableConfigs:[],
        topTab:1
        ...
    }
}
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
  }
}
```

#### 注意两个细节：

第一，当`v-for`跟`v-if`指令同时使用的时候要用`<template>`包起来

> VUE不推荐在同一元素上使用 `v-if` 和 `v-for`，而如果你的目的是有条件地跳过循环的执行，那么可以将 `v-if` 置于外层元素 (或`<template>` )上 

第二，如果你需要通过配置tableConfig在两张表格之间切换，千万记住：给你的`<el-table>`标签绑定key，这样vue在进行diff时才不会出错。

> `key` 的特殊属性主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 key 时，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。
>
> 有相同父元素的子元素必须有**独特的 key**。重复的 key 会造成渲染错误。

至此，我们搞定了这个需求的第一个问题，两个表格页在同一个页面展示，顺便掌握了一种更直观的`<el-table>`写法。

PS：当然，你也可以选择直接将一整个`<el-table>`二次封装，使用渲染函数来传入你的自定义列，那我觉得那样太复杂了，不利于后来人的维护（增加学习以及阅读成本），实现的效果跟直接这样写基本一样。

