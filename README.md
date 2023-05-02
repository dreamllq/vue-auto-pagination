# lc-vue-auto-pagination

基于 element-plus 自动数据获取/分页组件

## 安装

```
npm i lc-vue-auto-pagination
```

## 文档

[unpkg](https://unpkg.com/lc-vue-auto-pagination/docs/.vitepress/dist/index.html)

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