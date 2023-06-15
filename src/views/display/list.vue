<template>
  <div class="goodsListcontainer">
    <div class="topBox">
      <el-input v-model="keyword" placeholder="搜索商品名称" clearable maxlength="50" @clear="getList(2)"> </el-input>
      <el-button icon="Search" type="primary" @click="getList(2)"></el-button>
    </div>
    <el-table :data="list" border>
      <el-table-column prop="product_id" label="商品id" align="center" min-width="50"></el-table-column>
      <el-table-column
        prop="product_name"
        label="商品名称"
        align="center"
        min-width="140"
        show-overflow-tooltip
      ></el-table-column>
      <el-table-column prop="sale_price" label="商品价格" align="center" min-width="80"></el-table-column>
      <el-table-column label="商品图片" align="center" min-width="130">
        <template #default="scope">
          <el-image class="product_image" :src="scope.row.product_image" fit="cover" />
        </template>
      </el-table-column>
      <el-table-column prop="product_desc" label="商品描述" align="center" min-width="180"></el-table-column>
      <el-table-column prop="product_detail" label="商品详情" align="center" min-width="240"></el-table-column>
      <el-table-column prop="create_time" label="发布时间" align="center" min-width="150"></el-table-column>
      <el-table-column prop="update_time" label="更新时间" align="center" min-width="150"></el-table-column>
    </el-table>
    <div class="pagination">
      <el-pagination
        background
        layout="total, prev, pager, next,jumper"
        :current-page="page"
        :page-size="row"
        :total="records"
        :page-count="total"
        @current-change="handlePageChange"
      />
    </div>
  </div>
</template>
<script>
import { ElMessage, ElMessageBox } from 'element-plus'
export default {
  data() {
    return {
      searchType: '-1',
      list: [],
      page: 1,
      row: 4,
      records: 1,
      total: 1,
      keyword: '',
      value: '',
      shenHe: false,
      isable: true,
      editVisible: false,
      activeObj: {
        index: 0,
        data: {}
      },
      form: []
    }
  },
  methods: {
    getList(e) {
      if (e == 2) {
        if (this.value != this.keyword) {
          this.value = this.keyword
          this.page = 1
          this.list = []
        } else {
          return
        }
      }
      this.$get('good/list', {
        page: this.page,
        row: this.row,
        keyword: this.value
      }).then((res) => {
        if (res.code == 200) {
          this.list = res.data
          console.log(this.list)
          this.records = res.records
          this.total = res.total
        } else {
          ElMessage.error(res.msg)
        }
      })
    },
    handleEdit(index, row) {
      this.editVisible = true
      this.form = JSON.parse(JSON.stringify(row))
    },
    // 编辑
    saveEdit() {
      this.$post('good/edit', {
        product_id: this.form.product_id,
        product_name: this.form.product_name,
        sale_price: this.form.sale_price
      }).then((res) => {
        if (res.code == 200) {
          this.editVisible = false
          ElMessage.success('编辑成功')
          this.getList()
        } else {
          ElMessage.error(res.msg)
        }
      })
    },
    examine(item, index) {
      this.shenHe = true
      this.activeObj.item = item
      this.activeObj.index = index
    },
    handlePageChange(val) {
      this.page = val
      this.getList()
    },
    change(item, index, type) {
      this.$post('good/status', {
        product_id: item.product_id,
        status: type
      }).then((res) => {
        if (res.code == 200) {
          this.list[index].status = res.data.status
        } else {
          ElMessage.error(res.msg)
        }
      })
    },
    confirm() {
      this.$post('good/status', {
        product_id: this.activeObj.item.product_id,
        status: this.isable ? 2 : 1
      }).then((res) => {
        if (res.code == 200) {
          this.list[this.activeObj.index].status = res.data.status
          this.shenHe = false
        } else {
          ElMessage.error(res.msg)
        }
      })
    },
    handleDelete(id, index) {
      ElMessageBox.confirm('确定要删除吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
        buttonSize: 'default'
      })
        .then(() => {
          this.$post('good/status', {
            product_id: id,
            status: 4
          }).then((res) => {
            if (res.code == 200) {
              this.list[index].status = res.data.status
            } else {
              ElMessage.error(res.msg)
            }
          })
        })
        .catch(() => {})
    }
  },
  created() {
    this.getList()
  }
}
</script>
<style lang="scss">
.goodsListcontainer {
  .topBox {
    display: flex;
    align-items: center;
    margin: 0 0 15px;
    .el-input {
      width: 400px;
    }
    .el-input__inner {
      border-radius: 0;
    }
    .el-button {
      border-radius: 0 3px 3px 0;
    }
    .el-select {
      width: 100px;
      .el-input {
        width: 100px;
        input {
          border-radius: 3px 0 0 3px;
        }
      }
    }
  }
  .el-dialog__footer {
    padding-bottom: 10px;
  }
}
</style>
