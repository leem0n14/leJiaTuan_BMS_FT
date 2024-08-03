<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('app:coach:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('app:coach:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <!-- <el-table-column prop="coachId" header-align="center" align="center" label="教练ID">
      </el-table-column> -->
      <!-- <el-table-column prop="userId" header-align="center" align="center" label="用户id">
      </el-table-column> -->
      <el-table-column prop="statu" header-align="center" align="center" width="120" label="是否在小程序展示">
        <template slot-scope="scope">
          <!-- <div>{{ scope.row.statu == 0 ? '否' : '是' }}</div> -->
          <el-switch v-model="scope.row.statu" :active-value="1" :inactive-value="0" active-text="是" inactive-text="否"
            @change="display(scope.row.statu, scope.row.coachId)">
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column prop="attestation" header-align="center" align="center" label="是否认证">
        <template slot-scope="scope">
          <div>{{ scope.row.attestation == 0 ? '否' : '是' }}</div>
        </template>
      </el-table-column>
      <el-table-column prop="numbers" header-align="center" align="center" label="显示顺序" width="50">
      </el-table-column>
      <el-table-column prop="coachPhoto" header-align="center" align="center" label="教练照片">
        <template slot-scope="scope">
          <!-- <el-image style="width: 100px; height: 100px" fit="contain" :src="cdn + scope.row.coachPhoto"
            :preview-src-list="[cdn + scope.row.coachPhoto]">
          </el-image> -->
          <el-popover placement="left" trigger="click">
            <img slot="reference" :src="cdn + scope.row.coachPhoto" alt="" style="width:70px;">
            <img :src="cdn + scope.row.coachPhoto" alt="" style="width:1000px;">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="coachName" header-align="center" align="center" label="教练姓名">
      </el-table-column>
      <el-table-column prop="coachSex" header-align="center" align="center" label="教练性别">
      </el-table-column>
      <el-table-column prop="coachPhone" header-align="center" align="center" label="教练电话">
      </el-table-column>
      <el-table-column prop="coachType" header-align="center" align="center" label="教练性质">
      </el-table-column>
      <el-table-column prop="school" header-align="center" align="center" label="所属驾校">
      </el-table-column>
      <el-table-column prop="stuNum" header-align="center" align="center" label="教学学员数量">
      </el-table-column>
      <el-table-column prop="evaluation" header-align="center" align="center" label="评分">
        <!-- <template slot-scope="scope">
          <el-rate v-model="scope.evaluation"></el-rate>
          <el-rate v-model="scope.row.evaluation" disabled="true" show-score text-color="#ff9900"
            score-template="{scope.row.evaluation}">
          </el-rate>
        </template> -->
      </el-table-column>
      <el-table-column prop="evaluationNum" header-align="center" align="center" label="评分人数">
      </el-table-column>
      <el-table-column prop="evaluationSum" header-align="center" align="center" label="总评分">
      </el-table-column>
      <el-table-column prop="city" header-align="center" align="center" label="教练所在城市">
      </el-table-column>
      <el-table-column prop="age" header-align="center" align="center" label="教龄">
      </el-table-column>
      <el-table-column prop="introduction" header-align="center" align="center" label="简介">
        <template slot-scope="scope">
          <div class="introduction">{{
            scope.row.introduction }}</div>
        </template>
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.coachId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.coachId)">删除</el-button>
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
import AddOrUpdate from './coach-add-or-update'
export default {
  data() {
    return {
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/coach/',
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
    //是否在小程序显示
    display(val, coachId) {
      // console.log(val);
      this.$http({
        url: this.$http.adornUrl(`/sys/coach/update`),
        method: 'post',
        data: this.$http.adornData({
          'statu': val,
          coachId
        })
      }).then((res) => {
        console.log(res);
        this.getDataList()
      })
    },

    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/coach/list'),
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
        return item.coachId
      })
      this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/sys/coach/delete'),
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


<style lang="scss">
.introduction {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;
}
</style>