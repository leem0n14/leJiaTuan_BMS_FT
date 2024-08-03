<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <!-- <el-button v-if="isAuth('app:carpool:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <el-button v-if="isAuth('app:carpool:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
        <el-button type="warning" @click="showExcel = true" :disabled="dataListSelections.length <= 0">导出</el-button>
      </el-form-item>

      <el-form-item>
        <!-- <el-input v-model="fileName" placeholder="输入导出的excel文件名"
          style="display: inline-block;margin-bottom: 5px;"></el-input>
        <el-input v-model="title" placeholder="输入导出的excel表格标题"
          style="display: inline-block;margin-bottom: 5px;"></el-input>
        <div style="display: inline-block;">
          <download-excel class="export-excel-wrapper" :data="DetailsForm" :fields="json_fields" :header="title"
            :name="title">
            <el-button type="success" v-if="fileName != '' && !isExcel">导出</el-button>
            <el-tag type="success" v-else>选择要导出的数据和输入标题后显示导出按钮</el-tag>
          </download-excel>
        </div> -->
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="id" header-align="center" align="center" label="商户订单号">
      </el-table-column>
      <el-table-column prop="orderId" header-align="center" align="center" label="微信分账单号">
      </el-table-column>
      <el-table-column prop="outOrderNo" header-align="center" align="center" label="微信支付订单号">
      </el-table-column>
      <el-table-column prop="startPlace" header-align="center" align="center" label="起点">
      </el-table-column>
      <el-table-column prop="endPlace" header-align="center" align="center" label="终点">
      </el-table-column>
      <el-table-column prop="fqUserid" header-align="center" align="center" label="发起用户">
      </el-table-column>
      <el-table-column prop="fqName" header-align="center" align="center" label="发起人姓名">
      </el-table-column>
      <el-table-column prop="fqPhone" header-align="center" align="center" label="发起人电话">
      </el-table-column>
      <el-table-column prop="jdUserid" header-align="center" align="center" label="接单用户">
      </el-table-column>
      <el-table-column prop="jdName" header-align="center" align="center" label="接单姓名">
      </el-table-column>
      <el-table-column prop="jdPhone" header-align="center" align="center" label="接单电话">
      </el-table-column>
      <el-table-column prop="startTime" header-align="center" align="center" label="发起时间">
      </el-table-column>
      <el-table-column prop="endTime" header-align="center" align="center" label="结束时间">
      </el-table-column>
      <el-table-column prop="statu" header-align="center" align="center" label="是否拼车成功">
        <template slot-scope="scope">
          <div>
            {{ isSuccessPc(scope.row.statu) }}
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="price" header-align="center" align="center" label="价格">
      </el-table-column>
      <el-table-column prop="carId" header-align="center" align="center" label="车牌号">
      </el-table-column>
      <el-table-column prop="carType" header-align="center" align="center" label="车型">
      </el-table-column>
      <el-table-column prop="time" header-align="center" align="center" label="出发时间">
      </el-table-column>
      <el-table-column prop="result" header-align="center" align="center" label="分账结果">
      </el-table-column>
      <el-table-column prop="failReason" header-align="center" align="center" label="分账失败原因">
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <!-- <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button> -->
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <el-dialog title="导出excel" :visible.sync="showExcel" width="50%">
      <el-input v-model="fileName" placeholder="输入导出的excel文件名"
        style="display: inline-block;margin-bottom: 5px;"></el-input>
      <el-input v-model="title" placeholder="输入导出的excel表格标题" style="display: inline-block;margin-bottom: 5px;"></el-input>
      <div style="display: flex;flex-direction: column;align-items: flex-end;">
        <download-excel class="export-excel-wrapper" type="xls" :data="DetailsForm" :fields="json_fields" :header="title"
          :name="title" :before-generate="generate" :before-finish="finish">
          <el-button style="margin-top: 30px;" type="success" v-if="fileName != '' && !isExcel">导出</el-button>
          <el-tag type="success" v-else>选择要导出的数据和输入标题后显示导出按钮</el-tag>
        </download-excel>
      </div>

    </el-dialog>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from './carpool-add-or-update'
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
      addOrUpdateVisible: false,
      showExcel: false,
      fileName: '拼车.xls',
      title: "拼车",
      json_fields: {
        "商户订单号": 'id',
        "微信分账单号": 'orderId',
        "微信字符订单号": 'outOrderNo',
        "起点": 'startPlace',
        "终点": 'endPlace',
        "发起用户": 'fqUserid',
        "发起人姓名": 'fqName',
        "发起人电话": 'fqPhone',
        "接单用户": 'jdUserid',
        "接单用户姓名": 'jdName',
        "接单电话": 'jdPhone',
        "发起时间": 'startTime',
        "结束时间": 'endTime',
        "是否拼车成功": {
          field: 'statu',
          callback: (e) => {
            if (e == 0) {
              return '成功'
            } else if (e == 1) {
              return '拼车中'
            } else if (e == 2) {
              return '拼车失败'
            } else if (e == 3) {
              return '未付款'
            } else if (e == 4) {
              return '付款失败'
            } else {
              return '错误'
            }
          }
        },
        "价格": 'price',
        "车牌号": 'carId',
        "车型": 'carType',
        "出发时间": 'time',
        "分账结果": 'result',
        "分账失败原因": 'failReason',
      },
      DetailsForm: [],
    }
  },
  components: {
    AddOrUpdate
  },
  activated() {
    this.getDataList()
  },
  computed: {
    isExcel() {
      if (this.DetailsForm.length == 0) {
        return true
      } else {
        return false
      }
    }
  },
  methods: {

    //excel下载进度
    finish() {
      // console.log(e);
      // this.$message('下载完成')
    },
    generate() {
      // console.log(e);
      this.$message('开始下载')
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/app/carpool/list'),
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

    // 是否拼车成功
    isSuccessPc(e) {
      if (e == 0) {
        return '成功'
      }
      else if (e == 1) {
        return '拼车中'
      } else if (e == 2) {
        return '拼车失败'
      } else if (e == 3) {
        return '未付款'
      } else if (e == 4) {
        return '付款失败'
      } else {
        return '错误'
      }
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
      console.log(val);
      this.DetailsForm = val
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
          url: this.$http.adornUrl('/app/carpool/delete'),
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
