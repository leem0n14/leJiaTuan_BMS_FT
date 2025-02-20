<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="驾校名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('app:school:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('app:school:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="schoolId" header-align="center" align="center" label="ID" width="50">
      </el-table-column>
      <el-table-column prop="numbers" header-align="center" align="center" label="显示顺序" width="50">
      </el-table-column>
      <el-table-column prop="schoolName" header-align="center" align="center" label="驾校名称">
      </el-table-column>
      <el-table-column prop="schoolPhoto" header-align="center" align="center" label="驾校照片">
        <template slot-scope="scope">
          <el-popover placement="left" trigger="click">
            <img slot="reference" :src="cdn + 'school/' + scope.row.schoolPhoto[0]" alt="" style="width:70px;height:70px">
            <el-carousel trigger="click" indicator-position="none" style="width:1000px;" height="600px">
              <el-carousel-item v-for="(item, index) in scope.row.schoolPhoto" :key="index">
                <img :src="cdn + 'school/' + item" alt=""
                  style="width:100%;height:100%;background-color: #f1f4f5;margin: 0 auto;">
              </el-carousel-item>
            </el-carousel>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="zsPhoto" header-align="center" align="center" label="证书照片">
        <template slot-scope="scope">
          <el-popover placement="left" trigger="click">
            <img slot="reference" :src="cdn + 'zs/' + scope.row.zsPhoto" alt="" style="width:70px;height:70px">
            <img :src="cdn + 'zs/' + scope.row.zsPhoto" alt="" style="width:700px;height:700px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="schoolPlace" header-align="center" align="center" label="驾校地址">
      </el-table-column>
      <el-table-column prop="introduction" header-align="center" align="center" label="驾校简介">
      </el-table-column>
      <el-table-column prop="facilities" header-align="center" align="center" label="配套设施">
      </el-table-column>
      <el-table-column prop="size" header-align="center" align="center" label="规模">
      </el-table-column>
      <el-table-column prop="examPlace" header-align="center" align="center" label="考场">
      </el-table-column>
      <el-table-column prop="latitude" header-align="center" align="center" label="驾校纬度">
      </el-table-column>
      <el-table-column prop="longitude" header-align="center" align="center" label="驾校经度">
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.schoolId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.schoolId)">删除</el-button>
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
import AddOrUpdate from './school-add-or-update'
export default {
  data() {
    return {
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/',
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
        url: this.$http.adornUrl('/sys/school/list'),
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
        return item.schoolId
      })
      this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/sys/school/delete'),
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
