<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <!-- <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input> -->
      </el-form-item>
      <el-form-item>
        <!-- <el-button @click="getDataList()">查询</el-button> -->
        <el-button v-if="isAuth('app:information:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('app:information:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :row-style="{ height: '100px' }" :data="dataList" border v-loading="dataListLoading"
      @selection-change="selectionChangeHandle" style="width: 100%;" >
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <!-- <el-table-column prop="id" header-align="center" align="center" label="">
      </el-table-column> -->
      <el-table-column prop="title" header-align="center" align="center" label="标题">
      </el-table-column>
      <el-table-column prop="photo" header-align="center" align="center" label="封面">
        <template slot-scope="scope">
          <img :src="`${cdn}/super/information/${scope.row.photo}`" alt="" style="width: 200px;overflow: scroll;">
        </template>
      </el-table-column>
      <el-table-column prop="text" header-align="center" align="center" label="富文本" >
        <template slot-scope="scope">
          <div v-html="scope.row.text" v-if="scope.row.text" style="max-height: 200px;"></div>
          <el-tag v-else> 无</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="subject" header-align="center" align="center" label="科目">
      </el-table-column>
      <el-table-column prop="type" header-align="center" align="center" label="类型栏目">
        <template slot-scope="scope">
          <el-tag type="info" v-if="scope.row.type === 1">考试秘籍</el-tag>
          <el-tag type="success " v-else-if="scope.row.type === 2">学车技巧</el-tag>
          <el-tag type="danger " v-else-if="scope.row.type === 3">答题技巧</el-tag>
          <div v-else>无</div>
        </template>
      </el-table-column>
      <el-table-column prop="status" header-align="center" align="center" label="是否显示">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 0">不显示</el-tag>
          <el-tag v-else-if="scope.row.status === 1">显示</el-tag>
          <el-tag v-else>error</el-tag>
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
import AddOrUpdate from './information-add-or-update'
export default {
  data() {
    return {
      cdn: window.SITE_CONFIG.baseUrl,
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
      console.log("----------------------")
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/information/list'),
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
          url: this.$http.adornUrl('/sys/information/delete'),
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
<style></style>