<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="用户名 商品名 订单类型" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <!-- <el-button v-if="isAuth('app:orders:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <el-button v-if="isAuth('app:orders:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
      <el-form-item>
        <el-date-picker clearable v-model="dataForm.time" type="date" placeholder="请选择日期" value-format="yyyy-MM-dd"
          @change="getDataList()">
        </el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-select clearable v-model="dataForm.statuKey" placeholder="请选择订单类型" @change="getDataList()">
          <el-option v-for="k in statuList" :key="k.statu" :label="k.label" :value="k.statu">
          </el-option>
        </el-select>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="id" header-align="center" align="center" label="订单ID">
      </el-table-column>
      <el-table-column prop="orderId" header-align="center" align="center" label="微信预付订单号">
      </el-table-column>
      <el-table-column prop="refundId" header-align="center" align="center" label="退款订单号">
        <template slot-scope="scope">
          <div v-if="scope.row.refundId">
            {{ scope.row.refundId }}
          </div>
          <div v-else>
            {{ '(退款成功后显示)' }}
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="refundReason" header-align="center" align="center" label="退款原因">
        <template slot-scope="scope">
          {{ scope.row.refundReason ? scope.row.refundReason : '(无)' }}
        </template>
      </el-table-column>
      <el-table-column prop="goodName" header-align="center" align="center" label="商品名">
      </el-table-column>
      <el-table-column prop="userId" header-align="center" align="center" label="用户ID">
      </el-table-column>
      <el-table-column prop="userName" header-align="center" align="center" label="用户名">
      </el-table-column>
      <!-- <el-table-column prop="groupId" header-align="center" align="center" label="拼团ID">
      </el-table-column> -->
      <el-table-column prop="number" header-align="center" align="center" label="下单数量">
      </el-table-column>
      <el-table-column prop="createTime" header-align="center" align="center" label="创建时间">
      </el-table-column>
      <el-table-column prop="orderTime" header-align="center" align="center" label="订单完成时间">
      </el-table-column>
      <el-table-column prop="statu" header-align="center" align="center" label="订单状态">
        <template slot-scope="scope">
          <el-tag>{{ orderStatu(scope.row.statu) }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="realPrice" header-align="center" align="center" label="实际付款金额">
        <template slot-scope="scope">
          <div>
            {{ scope.row.realPrice + '元' }}
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="type" header-align="center" align="center" label="订单类型">
        <template slot-scope="scope">
          <el-tag type="success">{{ orderType(scope.row.type) }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="failReason" label="拒绝退款原因" header-align="center" align="center">
        <template slot-scope="scope">
          {{ scope.row.failReason ? scope.row.failReason : '(无)' }}
        </template>
      </el-table-column>

      <el-table-column prop="endTime" header-align="center" align="center" label="订单设定结束时间">
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="200" label="操作">
        <template slot-scope="scope">
          <!-- <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button> -->
          <el-button-group>
            <el-button type="success" size="small" @click="refund(scope.row.id, 0)"
              v-if="scope.row.statu === 3">同意退款</el-button>
            <el-button type="danger" size="small" @click="refund(scope.row.id, 1)"
              v-if="scope.row.statu === 3">拒绝退款</el-button>
          </el-button-group>

          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>

    <el-dialog title="拒绝原因" :visible.sync="showWin" @close="closeWin()">
      <el-input v-model="failReason" placeholder="请输入内容"></el-input>
      <span slot="footer" class="dialog-footer">
        <el-button @click="showWin = false">取 消</el-button>
        <el-button type="primary" @click="refundRequest(id, '拒绝', '/sys/orders/update', 7)">确 定</el-button>
      </span>
    </el-dialog>

    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from './orders-add-or-update'
export default {
  data() {
    return {
      dataForm: {
        key: '',
        time: '',
        statuKey: '',
      },
      dataList: [],
      statuList: [{
        statu: 0,
        label: '未付款',
      }, {
        statu: 1,
        label: '交易完成'
      }, {
        statu: 2,
        label: '付款取消'
      }, {
        statu: 3,
        label: '申请退款'
      }, {
        statu: 4,
        label: '退款成功'
      }, {
        statu: 5,
        label: '拼团中'
      }, {
        statu: 6,
        label: '拼团成功'
      }, {
        statu: 7,
        label: '退款失败'
      }
      ],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      showWin: false,
      failReason: '',
      id: '',
    }
  },
  components: {
    AddOrUpdate
  },
  activated() {
    this.getDataList()
  },
  methods: {
    closeWin() {
      this.id = ''
      this.failReason = ''
    },
    refundRequest(id, msg, url, statu) {
      if (statu === 7 && this.failReason === '') {
        this.$message('请输入拒绝原因')
        return
      }
      this.$confirm(`你确定要${msg}订单'${id}'的退款吗`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl(url),
          method: 'post',
          data: this.$http.adornData({ id, statu, failReason: this.failReason })
        }).then(({ data }) => {
          // console.log('退款', data);
          if (data && data.code === 0) {
            this.$message.success(`已${msg}退款`)
            this.showWin = false
            this.getDataList()
          } else {
            this.$message(data.msg)
            this.getDataList()
          }
          this.getDataList()
        })
      })
    },
    //退款
    refund(id, type) {
      //type:0同意,1拒绝
      let msg
      let url
      let statu
      if (type && type == 0) {
        msg = '同意'
        url = '/app/prePay/refund'
        statu = 4
        this.refund(id, msg, url, statu)
      } else if (type && type == 1) {
        msg = '拒绝'
        url = '/sys/orders/update'
        statu = 7
        this.id = id
        this.showWin = true
      }

    },
    //订单类型
    orderType(e) {
      switch (e) {
        case "普通": return '普通订单';
        case "秒杀": return '秒杀订单';
        case "拼团": return '拼团订单';
        default: return '数据错误';
      }
    },
    //订单状态
    orderStatu(e) {
      switch (e) {
        case 0: return '未付款';
        case 1: return '交易完成';
        case 2: return '付款取消';
        case 3: return '申请退款';
        case 4: return '退款成功';
        case 5: return '拼团中';
        case 6: return '拼团成功';
        case 7: return '退款失败';
        default: return '数据错误';
      }
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/orders/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          'key': this.dataForm.key,
          'statuKey': this.dataForm.statuKey,
          'time': this.dataForm.time,
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
          url: this.$http.adornUrl('/sys/orders/delete'),
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
