# lc-vue-auto-pagination

## 安装

```
npm i lc-vue-auto-pagination
```

## 基础用法

<script setup>
  import {AutoPagination} from 'lc-vue-auto-pagination'
  import {ref,onMounted } from 'vue'

  const pagination = ref();

  onMounted(() => {
    pagination.value.goFirstPage();
  })

  const fetchData = ({ pageNo, pageSize }) =>{
    return  {list: [{name: '张三', age: 18}, {name: '李四', age: 19}], total: 100}
  }
</script>


<div  style="height: 400px;">
  <AutoPagination :fetch-data="fetchData" ref="pagination">
    <template #default="{data, indexMethod}">
      <el-table :data="data">
        <el-table-column type="index" :index="indexMethod"></el-table-column>
        <el-table-column prop="name" label="姓名"></el-table-column>
        <el-table-column prop="age" label="年龄"></el-table-column>
      </el-table>
    </template>
  </AutoPagination>
</div>

```vue
<script setup>
  import {AutoPagination} from 'lc-vue-auto-pagination'
  import {ref,onMounted } from 'vue'

  const pagination = ref();

  onMounted(() => {
    pagination.value.goFirstPage();
  })

  const fetchData = ({ pageNo, pageSize }) =>{
    return  {list: [{name: '张三', age: 18}, {name: '李四', age: 19}], total: 100}
  }
</script>


<template>
  <div style="height: 400px;">
    <AutoPagination :fetch-data="fetchData" ref="pagination">
      <template #default="{data, indexMethod}">
        <el-table :data="data">
          <el-table-column type="index" :index="indexMethod"></el-table-column>
          <el-table-column prop="name" label="姓名"></el-table-column>
          <el-table-column prop="age" label="年龄"></el-table-column>
        </el-table>
      </template>
    </AutoPagination>
  </div>
</template>
```

## Api

### Attributes

| 属性名 | 说明 | 类型 | 默认值 |
| ---- | ---- | ---- | ---- |
| fetchData | 数据获取 | (option?:\{pageNo:number, pageSize: number\})=>\{list: any[], total: number \} | () => (\{ list: [], total: 0 \}) |
| pageSizes | 每页数量可选列表 | number[] | [10, 20, 50, 100] |
| defaultPageSize | 默认每页数量 | number | 20 |
| prePageChanged | 页面切换前校验 | (newVal:number, oldVal:number)=>boolean | () => true |
| noPagination | 是否无翻页场景 | boolean | false |
| autoInit | 是否自动调用fetchData | boolean | false |


## Slots

| 插槽名 | 说明 | 参数 |
| ---- | ---- | ---- | 
| default | 自定义内容 | \{data:any[], total: number, pageSize: number, currentPage: number, indexMethod: (index: number) => number \} |

## Exposes

| 名称 | 描述 | 类型 |
| ---- | ---- | ---- |
| refresh | 刷新数据 | ()=>void |
| goFirstPage | 跳转第一页 | ()=>void |
| goLastPage | 跳转最后一页 | ()=>void |
| goNextPage | 跳转下一页 | ()=>void |
| goPrevPage | 跳转前一页 | ()=>void |
| go | 跳转指定页 | (pageNumber: number) => void |