<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key1" placeholder="教练姓名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.key2" placeholder="场地" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-date-picker format="yyyy-MM-dd" value-format="yyyy-MM-dd" v-model="dataForm.key3" type="date"
          placeholder="选择日期">
        </el-date-picker>
        <!-- <el-input v-model="dataForm.key3" placeholder="时间" clearable></el-input> -->
      </el-form-item>
      <el-form-item>
        <el-select v-model="dataForm.key4" clearable placeholder="请选择">
          <el-option v-for="item in ['未审核', '准时', '迟到']" :key="item" :label="item" :value="item">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <!-- <el-button v-if="isAuth('app:coachregister:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <el-button v-if="isAuth('app:coachregister:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <!-- <el-table-column prop="id" header-align="center" align="center" label="ID">
      </el-table-column> -->
      <el-table-column prop="coachId" header-align="center" align="center" label="教练ID" width="50">
      </el-table-column>
      <el-table-column prop="coachName" header-align="center" align="center" label="教练姓名" width="100">
      </el-table-column>
      <el-table-column prop="registerTime" header-align="center" align="center" label="签到时间" width="200">
      </el-table-column>
      <el-table-column prop="ground" header-align="center" align="center" label="打卡场地" width="200">
      </el-table-column>
      <el-table-column prop="site" header-align="center" align="center" label="打卡地点">
      </el-table-column>
      <el-table-column prop="statu" header-align="center" align="center" label="签到状态" width="100">
        <template slot-scope="scope">
          <div>
            <el-tag type="success" v-if="scope.row.statu === '准时'">{{ scope.row.statu }}</el-tag>
            <el-tag type="danger" v-else-if="scope.row.statu === '迟到'">{{ scope.row.statu }}</el-tag>
            <el-tag v-else>{{ scope.row.statu }}</el-tag>
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="rePhoto" header-align="center" align="center" label="打卡图片" width="100">
        <template slot-scope="scope">
          <el-popover placement="left" trigger="click">
            <img slot="reference" :src="cdn + scope.row.rePhoto" alt="" style="width:70px;height:70px">
            <img :src="cdn + scope.row.rePhoto" alt="" style="width:1000px;height:1000px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="longitude" header-align="center" align="center" label="经度" width="125">
      </el-table-column>
      <el-table-column prop="latitude" header-align="center" align="center" label="纬度" width="125">
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
import AddOrUpdate from './coachregister-add-or-update'
export default {
  data() {
    return {
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/check/',
      dataForm: {
        key1: '',
        key2: '',
        key3: '',
        key4: '',
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
        url: this.$http.adornUrl('/sys/coachregister/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          'key1': this.dataForm.key1 !== '' ? this.dataForm.key1 : null,
          'key2': this.dataForm.key2 !== '' ? this.dataForm.key2 : null,
          'key3': this.dataForm.key3 !== '' ? this.dataForm.key3 : null,
          'key4': this.dataForm.key4 !== '' ? this.dataForm.key4 : null,
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
          url: this.$http.adornUrl('/sys/coachregister/delete'),
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
