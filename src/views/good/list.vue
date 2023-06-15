<template>
  <div class="goodsListcontainer">
    <div class="topBox">
      <el-input v-model="keyword" placeholder="搜索商品名称" clearable maxlength="50" @clear="getList(2)">
        <template #prepend>
          <el-select v-model="searchType" placeholder="Select" @change="getList(2)">
            <el-option label="全部" value="-1" />
            <el-option label="审核中" value="0" />
            <el-option label="审核失败" value="1" />
            <el-option label="销售中" value="2" />
            <el-option label="下架" value="3" />
            <el-option label="删除" value="4" />
          </el-select>
        </template>
        <template #append>
          <el-button icon="Search" type="primary" @click="SearchgetList(2)" />
        </template>
      </el-input>
    </div>
    <el-table :data="list" border>
      <el-table-column prop="product_id" label="商品id" align="center" min-width="80"></el-table-column>
      <el-table-column
        prop="product_name"
        label="商品名称"
        align="center"
        min-width="140"
        show-overflow-tooltip
      ></el-table-column>
      <el-table-column prop="origin_price" label="商品原价" align="center" min-width="100"></el-table-column>
      <el-table-column prop="sale_price" label="销售价格" align="center" min-width="100"></el-table-column>
      <el-table-column prop="store_name" label="店铺(发布者)" align="center" min-width="100"></el-table-column>
      <el-table-column prop="like" label="收藏(人数)" align="center" min-width="100"></el-table-column>
      <el-table-column label="商品状态" align="center" min-width="100">
        <template #default="scope">
          <el-tag
            :type="
              scope.row.status == 2
                ? 'success'
                : scope.row.status == 0
                ? 'info'
                : scope.row.status == 1
                ? 'warning'
                : 'danger'
            "
          >
            {{
              scope.row.status == 0
                ? '审核中'
                : scope.row.status == 1
                ? '审核失败'
                : scope.row.status == 2
                ? '销售中'
                : scope.row.status == 3
                ? '下架'
                : '删除'
            }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="create_time" label="发布时间" align="center" min-width="180"></el-table-column>
      <el-table-column prop="update_time" label="更新时间" align="center" min-width="180"></el-table-column>
      <el-table-column label="操作" min-width="180" align="center">
        <template #default="scope">
          <el-button type="text" icon="Edit" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button
            type="text"
            icon="Setting"
            @click="examine(scope.row, scope.$index)"
            style="color: #5c9a2c"
            v-if="scope.row.status == 0"
            >审核</el-button
          >
          <el-button
            type="text"
            icon="Setting"
            @click="change(scope.row, scope.$index, 2)"
            style="color: #5c9a2c"
            v-if="scope.row.status == 3"
            >上架</el-button
          >
          <el-button
            type="text"
            icon="Setting"
            @click="change(scope.row, scope.$index, 3)"
            style="color: #5c9a2c"
            v-if="scope.row.status == 4"
            >恢复</el-button
          >
          <el-button
            type="text"
            icon="Delete"
            @click="handleDelete(scope.row.product_id, scope.$index)"
            class="red"
            v-if="scope.row.status != 4"
            >删除</el-button
          >
        </template>
      </el-table-column>
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
    <!-- 审核 -->
    <el-dialog title="审核" v-model="shenHe" width="380px">
      <div class="option">
        <el-radio v-model="isable" :label="true" size="default" border>通过</el-radio>
        <el-radio v-model="isable" :label="false" size="default" border>不通过</el-radio>
      </div>
      <template #footer>
        <div class="el-dialog__footer">
          <el-button @click="shenHe = false">取 消</el-button>
          <el-button type="primary" @click="confirm">确 定</el-button>
        </div>
      </template>
    </el-dialog>
    <!-- 编辑框 -->
    <el-dialog title="编辑" v-model="editVisible" width="380px">
      <el-form label-width="70px">
        <el-form-item label="商品名称">
          <el-input v-model="form.product_name" maxlength="11"></el-input>
        </el-form-item>
        <el-form-item label="销售价格">
          <el-input v-model="form.sale_price" maxlength="8"></el-input>
        </el-form-item>
      </el-form>
      <div class="el-dialog__footer">
        <el-button @click="editVisible = false">取 消</el-button>
        <el-button type="primary" @click="saveEdit">确 定</el-button>
      </div>
    </el-dialog>
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
      row: 10,
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
        this.page = 1
      }
      let data = {
        page: this.page,
        row: this.row,
        keyword: this.value
      }
      if (this.searchType > -1) {
        data.status = this.searchType
      }
      this.$get('good/list', data).then((res) => {
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
    SearchgetList(e) {
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
