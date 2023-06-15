<template>
  <div class="container">
    <el-table :data="list" border header-cell-class-name="table-header">
      <el-table-column prop="user_name" label="收货人" align="center" min-width="50" />
      <el-table-column prop="order_no" label="订单编号" align="center" min-width="70" />
      <el-table-column prop="product_name" label="商品名称" align="center" min-width="100" />
      <el-table-column prop="pay_time" label="付款时间" align="center" min-width="110" />
      <el-table-column prop="pay_money" label="付款金额" align="center" min-width="70" />
      <el-table-column prop="create_time" label="订单生成时间" align="center" min-width="110" />
      <el-table-column prop="send_time" label="发货时间" align="center" min-width="110" />
      <el-table-column prop="receive_time" label="收货时间" align="center" min-width="110" />
      <el-table-column label="操作" min-width="100" align="center" fixed="right">
        <template #default="scope">
          <el-button type="text" icon="Delete" @click="handleDelete(scope.row.order_id)">删除订单</el-button>
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
import { useRouter } from 'vue-router'
import { ElMessage, ElMessageBox } from 'element-plus'
export default defineComponent({
  setup() {
    const that = getCurrentInstance().appContext.config.globalProperties
    const router = useRouter()
    let list = ref([])
    let page = ref(1)
    let row = ref(10)
    let records = ref(1)
    let total = ref(1)
    const getList = () => {
      that
        .$get('order/list', {
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
    const handleDelete = (order_id) => {
      ElMessageBox.confirm('确定要删除吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
        buttonSize: 'default'
      })
        .then(() => {
          that
            .$post('order/delete', {
              order_id: order_id
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
