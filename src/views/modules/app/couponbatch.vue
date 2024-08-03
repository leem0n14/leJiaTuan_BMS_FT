<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="优惠券名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('app:couponbatch:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('app:couponbatch:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <!-- <el-table-column prop="batchId" header-align="center" align="center" label="批次ID">
      </el-table-column> -->
      <el-table-column prop="batchName" header-align="center" align="center" label="优惠券名称">
      </el-table-column>
      <el-table-column prop="totalCount" header-align="center" align="center" label="总数量">
      </el-table-column>
      <el-table-column prop="assignCount" header-align="center" align="center" label="已领取数量">
      </el-table-column>
      <el-table-column prop="usedCount" header-align="center" align="center" label="已使用数量">
      </el-table-column>
      <el-table-column prop="status" header-align="center" align="center" label="小程序是否显示">
        <template slot-scope="scope">
          <div v-if="scope.row.status == 1">{{ '是' }}</div>
          <div v-else-if="scope.row.status == 0">{{ '否' }}</div>
        </template>
      </el-table-column>
      <el-table-column prop="type" header-align="center" align="center" label="优惠券类型">
        <template slot-scope="scope">
          <div v-if="scope.row.type == 0">全场可用</div>
          <div v-else-if="scope.row.type == 1">指定驾校</div>
          <div v-else-if="scope.row.type == 2">指定教练</div>
        </template>
      </el-table-column>
      <el-table-column prop="appointName" header-align="center" align="center" label="指定使用">
      </el-table-column>
      <el-table-column prop="ruleContent" header-align="center" align="center" label="规则内容">
      </el-table-column>
      <el-table-column prop="threshold" header-align="center" align="center" label="使用门槛">
      </el-table-column>
      <el-table-column prop="amount" header-align="center" align="center" label="优惠金额">
      </el-table-column>
      <el-table-column prop="receiveCount" header-align="center" align="center" label="每个用户可以领取的数量">
      </el-table-column>
      <el-table-column prop="receiveStartedAt" header-align="center" align="center" label="领取开始时间">
      </el-table-column>
      <el-table-column prop="receiveEndedAt" header-align="center" align="center" label="领取结束时间">
      </el-table-column>
      <el-table-column prop="useStartedAt" header-align="center" align="center" label="使用开始时间">
      </el-table-column>
      <el-table-column prop="useEndedAt" header-align="center" align="center" label="使用结束时间">
      </el-table-column>
      <el-table-column prop="pushCount" header-align="center" align="center" label="推送次数">
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="toPush(scope.row.batchId)">推送</el-button>
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.batchId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.batchId)">删除</el-button>
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
import AddOrUpdate from './couponbatch-add-or-update'
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
    //推送优惠券
    toPush(batchId) {
      this.$http({
        url: this.$http.adornUrl('/sys/couponbatch/push'),
        method: 'post',
        data: this.$http.adornData({
          batchId,
        })
      }).then(({ data }) => {
        console.log(data);
        if (data && data.code == 0) {
          this.$message({
            message: '推送成功',
            type: 'success',
          })

        } else {
          this.$message(data.msg)
        }
        this.getDataList()
      })
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/couponbatch/list'),
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
        return item.batchId
      })
      this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/sys/couponbatch/delete'),
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
