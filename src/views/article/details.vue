<template>
  <div class="container">
    <el-table :data="list" border header-cell-class-name="table-header">
      <el-table-column prop="id" label="ID" align="center" min-width="50"></el-table-column>
      <el-table-column
        prop="title"
        label="公告标题"
        align="center"
        min-width="150"
        show-overflow-tooltip
      ></el-table-column>
      <el-table-column prop="content" label="公告内容" align="center" min-width="350"></el-table-column>
      <el-table-column label="操作" min-width="100" align="center">
        <template #default="scope">
          <el-button type="text" icon="Delete" class="red" @click="handleDelete(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="pagination">
      <el-pagination
        background
        layout="total, prev, pager, next"
        :current-page="page"
        :page-size="row"
        :total="records"
        :page-count="total"
        @current-change="handlePageChange"
      />
    </div>
  </div>
</template>
<script lang="ts">
import { defineComponent, getCurrentInstance, ref } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import { useRouter } from 'vue-router'
export default defineComponent({
  setup() {
    const that = getCurrentInstance().appContext.config.globalProperties
    const router = useRouter()
    let list = ref([])
    let page = ref(1)
    let row = ref(8)
    let records = ref(1)
    let total = ref(1)
    const getList = () => {
      that
        .$get('article/list', {
          page: page.value,
          row: row.value
        })
        .then((res) => {
          if (res.code == 200) {
            list.value = res.data
            records.value = res.records
            total.value = res.total
          } else {
            ElMessage.error(res.msg)
          }
        })
    }
    const handleDelete = (id) => {
      ElMessageBox.confirm('确定要删除吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
        buttonSize: 'default'
      })
        .then(() => {
          that
            .$post('article/delete', {
              id: id
            })
            .then((res) => {
              if (res.code == 200) {
                ElMessage.success('删除成功')
                page.value = 1
                getList()
              } else {
                ElMessage.error(res.msg)
              }
            })
        })
        .catch(() => {})
    }
    const handlePageChange = (val) => {
      page.value = val
      getList()
    }
    getList()

    return {
      page,
      row,
      records,
      total,
      list,
      getList,
      handleDelete,
      handlePageChange
    }
  }
})
</script>
<style lang="scss" scoped>
.container {
  .el-table {
    margin: 20px 0 0;
  }
}
</style>
