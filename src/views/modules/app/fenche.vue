<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key2" placeholder="车牌号" clearable></el-input>
      </el-form-item>
      <!-- <el-form-item>
        <el-input v-model="dataForm.key6" placeholder="车辆信息" clearable></el-input>
      </el-form-item> -->
      <el-form-item>
        <el-input v-model="dataForm.key1" placeholder="学生名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-date-picker format="yyyy-MM-dd" value-format="yyyy-MM-dd" v-model="dataForm.key3" type="date"
          placeholder="选择分车时间">
        </el-date-picker>
        <!-- <el-input v-model="dataForm.key3" placeholder="分车时间" clearable></el-input> -->
      </el-form-item>
      <el-form-item>
        <el-date-picker format="yyyy-MM-dd" value-format="yyyy-MM-dd" v-model="dataForm.key4" type="date"
          placeholder="选择完成时间">
        </el-date-picker>
        <!-- <el-input v-model="dataForm.key4" placeholder="完成时间" clearable></el-input> -->
      </el-form-item>
      <el-form-item>
        <el-select v-model="dataForm.key5" clearable placeholder="请选择状态">
          <el-option v-for="item in [{ name: '未练车', key: '0' }, { name: '练车完成', key: '1' }]" :key="item.key"
            :label="item.name" :value="item.key">
          </el-option>
        </el-select>
        <!-- <el-input v-model="dataForm.key5" placeholder="状态" clearable></el-input> -->
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <!-- <el-button v-if="isAuth('sys:fenche:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <el-button v-if="isAuth('sys:fenche:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="carId" header-align="center" align="center" label="车牌号">
      </el-table-column>
      <el-table-column prop="carType" header-align="center" align="center" label="用户填写的车辆信息">
      </el-table-column>
      <el-table-column prop="stuName" header-align="center" align="center" label="学生姓名">
      </el-table-column>
      <el-table-column prop="time" header-align="center" align="center" label="分车时间">
      </el-table-column>
      <el-table-column prop="finishTime" header-align="center" align="center" label="完成时间">
      </el-table-column>
      <el-table-column prop="status" header-align="center" align="center" label="状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status == 0">{{ '练车中' }}</el-tag>
          <el-tag v-else-if="scope.row.status == 1" type="success">{{ '练车完成' }}</el-tag>
          <el-tag v-else type="danger">{{ '错误' }}</el-tag>
        </template>
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
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from './fenche-add-or-update'
export default {
  data() {
    return {
      dataForm: {
        key2: '',//车牌号
        // key6: '',//车辆信息
        key1: '',//学生姓名
        key3: '',//分车时间
        key4: '',//完成时间
        key5: "",//状态
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
    statuTag(val, row) {
      console.log(val, row, 'statuTag');
      return true
    },
    changeTag(e) {
      console.log(e, 'changeTag');
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/fenche/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          'key1': this.dataForm.key1 != '' ? this.dataForm.key1 : null,
          'key2': this.dataForm.key2 != '' ? this.dataForm.key2 : null,
          'key3': this.dataForm.key3 != '' ? this.dataForm.key3 : null,
          'key4': this.dataForm.key4 != '' ? this.dataForm.key4 : null,
          'key5': this.dataForm.key5 != '' ? this.dataForm.key5 : null,
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
          url: this.$http.adornUrl('/sys/fenche/delete'),
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
