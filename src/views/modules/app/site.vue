<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('app:site:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('app:site:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <!-- <el-table-column prop="id" header-align="center" align="center" label="">
      </el-table-column> -->
      <el-table-column prop="siteName" header-align="center" align="center" label="场地名称">
      </el-table-column>
      <el-table-column prop="sitePhoto" header-align="center" align="center" label="场地首页照片">
        <template slot-scope="scope">
          <img :src="cdn + scope.row.sitePhoto" style="height: 75px;">
        </template>
      </el-table-column>
      <el-table-column prop="place" header-align="center" align="center" label="场地地址">
      </el-table-column>
      <el-table-column prop="schoolName" header-align="center" align="center" label="场地所属驾校">
        <template slot-scope="scope">
          <el-tag v-for="k, i in scope.row.schoolList" :key="k.schoolId" v-show="i <= 1">{{ k.schoolName }}</el-tag>
          <div v-if="scope.row.schoolList.length > 2">等{{ scope.row.schoolList.length + '个驾校' }}</div>
        </template>
      </el-table-column>
      <!-- <el-table-column prop="longitude" header-align="center" align="center" label="经度">
      </el-table-column>
      <el-table-column prop="latitude" header-align="center" align="center" label="纬度">
      </el-table-column> -->
      <el-table-column prop="signsTime" header-align="center" align="center" label="签到开始时间">
      </el-table-column>
      <el-table-column prop="signeTime" header-align="center" align="center" label="签到结束时间">
      </el-table-column>
      <el-table-column prop="leftsTime" header-align="center" align="center" label="签退开始时间">
      </el-table-column>
      <el-table-column prop="lefteTime" header-align="center" align="center" label="签退结束时间">
      </el-table-column>
      <el-table-column prop="introduction" header-align="center" align="center" label="场地简介">
      </el-table-column>
      <el-table-column prop="statu" header-align="center" align="center" label="是否开启">
        <template slot-scope="scope">
          <div>
            <el-switch v-model="scope.row.statu" :inactive-value="0" :active-value="1"
              @change="changeStatu(scope.row.statu, scope.row.id)"></el-switch>
          </div>
        </template>
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
import AddOrUpdate from './site-add-or-update'
export default {
  data() {
    return {
      dataForm: {
        key: ''
      },
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/site/',
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
    changeStatu(e, id) {
      // console.log(e);
      this.$http({
        url: this.$http.adornUrl(`/sys/site/update`),
        method: 'post',
        data: this.$http.adornData({
          statu: e,
          id,
        })
      }).then((data) => {
        this.getDataList()
        // console.log(data);
      })
    },

    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/site/list'),
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
          url: this.$http.adornUrl('/sys/site/delete'),
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
