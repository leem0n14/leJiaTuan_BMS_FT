<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="姓名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <!-- <el-button v-if="isAuth('app:coachaudit:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <el-button v-if="isAuth('app:coachaudit:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="name" header-align="center" align="center" label="姓名">
      </el-table-column>
      <el-table-column prop="phone" header-align="center" align="center" label="电话">
      </el-table-column>
      <el-table-column prop="introduce" header-align="center" align="center" label="介绍">
      </el-table-column>
      <!-- <el-table-column prop="addtime" header-align="center" align="center" label="提交时间">
      </el-table-column> -->
      <el-table-column prop="idNumber" header-align="center" align="center" label="身份证号">
      </el-table-column>
      <el-table-column prop="idPhotoz" header-align="center" align="center" label="身份证正面">
        <template slot-scope="scope">
          <img v-if="scope.row.idPhotoz" :src="`${cdn}/coach/${scope.row.idPhotoz}`" alt=""
            style="width: 200px; max-height: 200px;" @click="showImg(scope.row.idPhotoz)">
          <el-tag v-else>用户没有上传图片</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="idPhotof" header-align="center" align="center" label="身份证反面">
        <template slot-scope="scope">
          <img v-if="scope.row.idPhotof" :src="`${cdn}/coach/${scope.row.idPhotof}`" alt=""
            style="width: 200px;max-height: 200px;" @click="showImg(scope.row.idPhotof)">
          <el-tag v-else>用户没有上传图片</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="statu" header-align="center" align="center" label="审核状态">
        <template slot-scope="scope">
          <div>{{ hauditStatu(scope.row.statu) }}</div>
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
    <el-dialog :visible.sync="dialogVisible" width="50%" :before-close="handleClose">
      <img :src="imgUrl" style="width: 100%;">
    </el-dialog>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from './coachaudit-add-or-update'
export default {
  data() {
    return {
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super',
      dialogVisible: false,
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
      imgUrl: '',
    }
  },
  components: {
    AddOrUpdate
  },
  activated() {
    this.getDataList()
  },
  methods: {
    showImg(imgName) {
      this.imgUrl = `${this.cdn}/coach/${imgName}`
      this.dialogVisible = true
    },
    handleClose() {
      this.dialogVisible = false
      this.imgUrl = ''
    },
    hauditStatu(statu) {
      if (statu == 0) {
        return '审核中'
      }
      else if (statu == 1) {
        return '审核通过'
      }
      else if (statu == 2) {
        return '审核不通过'
      }
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/coachaudit/list'),
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
          url: this.$http.adornUrl('/sys/coachaudit/delete'),
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
