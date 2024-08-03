<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('app:student:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('app:student:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>

      <el-table-column prop="stuName" header-align="center" align="center" label="学员姓名">
      </el-table-column>
      <el-table-column prop="stuSex" header-align="center" align="center" label="学员性别">
      </el-table-column>
      <el-table-column prop="stuPhone" header-align="center" align="center" label="学员电话">
      </el-table-column>
      <el-table-column prop="classType" header-align="center" align="center" label="报班车型">
      </el-table-column>
      <!-- <el-table-column prop="coachId" header-align="center" align="center" label="教练ID">
      </el-table-column> -->
      <el-table-column prop="coachName" header-align="center" align="center" label="教练姓名">
      </el-table-column>
      <el-table-column prop="schoolName" header-align="center" align="center" label="驾校">
      </el-table-column>
      <el-table-column prop="stuLevel" header-align="center" align="center" label="学员等级">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.stuLevel == 0">普通</el-tag>
          <el-tag type="warning" v-else>vip</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="cardId" header-align="center" align="center" label="身份证号" width="160">
      </el-table-column>
      <el-table-column prop="cardAd" header-align="center" align="center" label="学员身份证地址" width="120">
      </el-table-column>
      <el-table-column prop="cardFront" header-align="center" align="center" label="身份证正面照" width="120">
        <template slot-scope="scope">
          <el-popover placement="left" trigger="click">
            <img slot="reference" :src="cdn + scope.row.cardFront" alt="" style="width:60px;height:60px">
            <img :src="cdn + scope.row.cardFront" alt="" style="width:1000px;height:1000px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="cardBack" header-align="center" align="center" label="身份证背面照" width="120">
        <template slot-scope="scope">
          <el-popover placement="left" trigger="click">
            <img slot="reference" :src="cdn + scope.row.cardBack" alt="" style="width:60px;height:60px">
            <img :src="cdn + scope.row.cardBack" alt="" style="width:1000px;height:1000px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="photo" header-align="center" align="center" label="半身照" width="120">
        <template slot-scope="scope">
          <el-popover placement="left" trigger="click">
            <img slot="reference" :src="cdn + scope.row.photo" alt="" style="width:60px;height:60px">
            <img :src="cdn + scope.row.photo" alt="" style="width:1000px;height:1000px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="tijianbiao" header-align="center" align="center" label="体检表照片" width="120">
        <template slot-scope="scope">
          <el-popover placement="left" trigger="click">
            <img slot="reference" :src="cdn + scope.row.tijianbiao" alt="" style="width:60px;height:60px">
            <img :src="cdn + scope.row.tijianbiao" alt="" style="width:1000px;height:1000px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="className" header-align="center" align="center" label="班型名称" width="120">
      </el-table-column>
      <el-table-column prop="addTime" header-align="center" align="center" label="报名时间" width="120">
        <template slot-scope="scope">
          <div>{{ scope.row.addTime.slice(0, 10) }}</div>
        </template>
      </el-table-column>
      <el-table-column prop="progress" header-align="center" align="center" label="学车进度">
        <template slot-scope="scope">
          <div>{{ getProgress(scope.row.progress) }}</div>
        </template>
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.stuId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.stuId)">删除</el-button>
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
import AddOrUpdate from './student-add-or-update'
export default {
  data() {
    return {
      url: window.SITE_CONFIG.baseUrl,
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/student/',
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
    getProgress(pro) {
      let v1 = pro / 10
      let v2 = pro % 10
      let c
      switch (v2) {
        case 0:
          c = '待考'; break
        case 1:
          c = '已完成'; break

      }
      switch (v1) {
        case 0:
          return '未开始'
        case 1:
          return `科目一(${c})`
        case 2:
          return `科目二(${c})`
        case 3:
          return `科目三(${c})`
        case 4:
          return `科目四(${c})`
      }

    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/student/list'),
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
        return item.stuId
      })
      this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/sys/student/delete'),
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
