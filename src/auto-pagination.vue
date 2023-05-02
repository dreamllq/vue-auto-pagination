<template>
  <div class='auto-pagination'>
    <div v-loading='loadingData' class='auto-pagination_data-container'>
      <slot
        :data='data'
        :total='total'
        :page-size='pageSize'
        :current-page='currentPage'
        :index-method='indexMethod'
      />
    </div>
    <div v-if='noPagination === false' class='auto-pagination_main'>
      <el-pagination
        v-model:current-page='currentPage'
        background
        :page-sizes='pageSizes'
        :page-size='pageSize'
        layout='total, sizes, prev, pager, next'
        :total='total'
        small
        @update:page-size='handleSizeChange'
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted, nextTick } from 'vue';

const props = defineProps({
  fetchData: {
    type: Function,
    default: () => ({
      list: [],
      total: 0
    })
  },
  pageSizes: {
    type: Array,
    default: () => [
      10,
      20,
      50,
      100
    ]
  },
  defaultPageSize: {
    type: Number,
    default: 20
  },
  prePageChanged: {
    type: Function,
    default: () => true
  },
  noPagination: {
    type: Boolean,
    default: false
  },
  autoInit: {
    type: Boolean,
    default: false
  }
});

const total = ref(0);
const pageSize = ref(props.defaultPageSize);
const currentPage = ref(1);
const data = ref([]);
const loadingData = ref(false);
const flagPage = ref(1);

watch(() => currentPage.value, async (newVal, oldVal) => {
  if (flagPage.value === newVal) return;
  let flag = await props.prePageChanged(newVal, oldVal);

  if (flag === true) {
    flagPage.value = newVal;
    refreshData();
  } else {
    currentPage.value = oldVal;
  }
});

onMounted(() => {
  if (props.autoInit === true) {
    refresh();
  }
});

const indexMethod = (index: number) => (currentPage.value - 1) * pageSize.value + index + 1;

const handleSizeChange = (ps: number) => {
  pageSize.value = ps;
  refreshData();
};

const refreshData = async () => {
  loadingData.value = true;
  if (props.noPagination === false) {
    let { list, total: t } = await props.fetchData({
      pageNo: currentPage,
      pageSize: pageSize
    });
    if (list.length > 0 || currentPage.value === 1) {
      data.value = [];
      await nextTick();
      data.value = list;
      total.value = t;
      loadingData.value = false;
    } else {
      if (currentPage.value * pageSize.value <= t) {
        loadingData.value = false;
        throw new Error('接口返回数据异常');
      }
      currentPage.value = Math.ceil(t / pageSize.value);
      refreshData();
    } 
  } else {
    let { list } = await props.fetchData();
    data.value = list;
    loadingData.value = false;
  }
};

const refresh = () => {
  refreshData();
};

const goFirstPage = () => {
  currentPage.value = 1;
  refreshData();
};

const goLastPage = () => {
  currentPage.value = Math.ceil(total.value / pageSize.value);
  refreshData();
};

const goNextPage = () => {
  if (Math.ceil(total.value / pageSize.value) === currentPage.value) {
    console.warn('已经是最后一页');
  } else {
    currentPage.value = currentPage.value + 1;
    refreshData();
  }
};

const goPrevPage = () => {
  if (currentPage.value === 1) {
    console.warn('已经在第一页');
  } else {
    currentPage.value = currentPage.value - 1;
    refreshData();
  }
};

const go = (pageNumber: number) => {
  if (pageNumber === currentPage.value) {
    console.warn('已经在当前页');
    return;
  }
  if (pageNumber > Math.ceil(total.value / pageSize.value)) {
    currentPage.value = Math.ceil(total.value / pageSize.value);
  } else {
    currentPage.value = pageNumber;
  }
  refreshData();
};

defineExpose({
  refresh,
  goFirstPage,
  goLastPage,
  goNextPage,
  goPrevPage,
  go
});
</script>

<style lang="scss" scoped>
.auto-pagination {
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;

  .auto-pagination_data-container{
    flex: 1;
    overflow: auto;
  }

  .auto-pagination_main {
    margin-top: 16px;
    flex: none;
  }
}
</style>

<style lang="scss">
.auto-pagination {
  .el-pagination{
    justify-content: flex-end;
  }
}
</style>
