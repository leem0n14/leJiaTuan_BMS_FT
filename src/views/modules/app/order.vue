<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('app:order:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('app:order:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="id" header-align="center" align="center" label="订单ID">
      </el-table-column>
      <el-table-column prop="orderId" header-align="center" align="center" label="">
      </el-table-column>
      <el-table-column prop="goodId" header-align="center" align="center" label="商品ID">
      </el-table-column>
      <el-table-column prop="goodName" header-align="center" align="center" label="商品名">
      </el-table-column>
      <el-table-column prop="userId" header-align="center" align="center" label="用户ID">
      </el-table-column>
      <el-table-column prop="userName" header-align="center" align="center" label="用户名">
      </el-table-column>
      <el-table-column prop="groupId" header-align="center" align="center" label="拼团ID">
      </el-table-column>
      <el-table-column prop="orderNum" header-align="center" align="center" label="下单数量">
      </el-table-column>
      <el-table-column prop="orderTime" header-align="center" align="center" label="下单时间">
      </el-table-column>
      <el-table-column prop="orderStatu" header-align="center" align="center"
        label="订单状态{ 0 未付款 10交易完成 20申请退款 21已退款 30 已取消 40 拼团中 50拼团成功}">
      </el-table-column>
      <el-table-column prop="realPrice" header-align="center" align="center" label="实际付款金额">
      </el-table-column>
      <el-table-column prop="ordderType" header-align="center" align="center" label="订单类型{0 普通订单  1秒杀订单 2拼团订单}">
      </el-table-column>
      <el-table-column prop="endtime" header-align="center" align="center" label="订单设定结束时间">
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from './order-add-or-update'
export default {
  data() {
    return {
      dataForm: {
        key: ''
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false
    }
  },
  components: {
    AddOrUpdate
  },
  activated() {
    this.getDataList()
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/app/order/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          'key': this.dataForm.key
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list
          this.totalPage = data.page.totalCount
        } else {
          this.dataList = []
          this.totalPage = 0
        }
        this.dataListLoading = false
      })
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val
      this.pageIndex = 1
      this.getDataList()
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val
      this.getDataList()
    },
    // 多选
    selectionChangeHandle(val) {
      this.dataListSelections = val
    },
    // 新增 / 修改
    addOrUpdateHandle(id) {
      this.addOrUpdateVisible = true
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id)
      })
    },
    // 删除
    deleteHandle(id) {
      var ids = id ? [id] : this.dataListSelections.map(item => {
        return item.id
      })
      this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/app/order/delete'),
          method: 'post',
          data: this.$http.adornData(ids, false)
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success',
              duration: 1500,
              onClose: () => {
                this.getDataList()
              }
            })
          } else {
            this.$message.error(data.msg)
          }
        })
      })
    }
  }
}
</script>
