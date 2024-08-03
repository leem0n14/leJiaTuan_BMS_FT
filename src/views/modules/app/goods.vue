<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('app:goods:save')" type="primary" @click="addOrUpdateHandle()">新增商品</el-button>
        <el-button v-if="isAuth('app:goods:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="goodId" header-align="center" align="center" label="班型ID">
      </el-table-column>
      <el-table-column prop="numbers" header-align="center" align="center" label="显示顺序" width="50">
      </el-table-column>
      <el-table-column prop="goodName" header-align="center" align="center" label="商品班型名">
      </el-table-column>
      <el-table-column prop="photo" header-align="center" align="center" label="商品首页照片" width="120">
        <template slot-scope="scope">
          <div>
            <img :src="cdn + '/super/goods/' + scope.row.photo" alt="" style="max-width:100px">
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="goodTitle" header-align="center" align="center" label="标题">
      </el-table-column>
      <!-- <el-table-column prop="goodDescribe" header-align="center" align="center" label="商品详情描述">
      </el-table-column> -->
      <el-table-column prop="goodTags" header-align="center" align="center" label="班型标签{课程车型}">
      </el-table-column>
      <el-table-column prop="goodPrice" header-align="center" align="center" label="商品班型价格">
      </el-table-column>
      <el-table-column prop="goodNum" header-align="center" align="center" label="商品数量">
      </el-table-column>
      <el-table-column prop="goodType" header-align="center" align="center" label="商品类型">
      </el-table-column>

      <el-table-column prop="goodPoint" header-align="center" align="center" label="赠送积分">
      </el-table-column>
      <el-table-column prop="ifCoach" header-align="center" align="center" label="是否是教练商品">
        <template slot-scope="scope">
          <div>{{ scope.row.ifCoach == 0 ? '否' : '是' }}</div>
        </template>
      </el-table-column>
      <!-- <el-table-column prop="coachId" header-align="center" align="center" label="教练id">
      </el-table-column> -->
      <!-- <el-table-column prop="schoolId" header-align="center" align="center" label="所属驾校ID">
      </el-table-column> -->
      <el-table-column prop="schoolName" header-align="center" align="center" label="所属驾校名称">
      </el-table-column>
      <!-- <el-table-column prop="createTime" header-align="center" align="center" label="创建时间">
      </el-table-column> -->
      <el-table-column prop="endTime" header-align="center" align="center" label="秒杀结束时间">
      </el-table-column>
      <el-table-column prop="groupNum" header-align="center" align="center" label="拼团所需人数">
      </el-table-column>
      <el-table-column prop="saleNum" header-align="center" align="center" label="销售数目">
      </el-table-column>
      <el-table-column prop="goodDescribe" header-align="center" align="center" label="商品富文本" width="120">
        <template slot-scope="scope">
          <div>
            <el-button type="text" @click="showFwb(scope.row.goodDescribe, scope.row.goodName)">查看富文本</el-button>
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="display" header-align="center" align="center" label="是否在首页展示">
        <template slot-scope="scope">
          <div>{{ scope.row.display == 0 ? '不展示' : '展示' }}</div>
          <el-switch v-model="scope.row.display" active-color="#409eff" inactive-color="#ff4949" :active-value="1"
            :inactive-value="0" @change="indexDisplay(scope.row.display, scope.row.goodId, scope.row.version)">
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column prop="goodStatu" header-align="center" align="center" label="上架状态" width="100">
        <template slot-scope="scope">
          <div>{{ scope.row.goodStatu == 0 ? '已下架' : '已上架' }}</div>
          <el-switch v-model="scope.row.goodStatu" :active-value="1" :inactive-value="0" active-color="#13ce66"
            inactive-color="#ff4949" @change="putaway(scope.row.goodStatu, scope.row.goodId, scope.row.version)">
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.goodId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.goodId)">删除</el-button>
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
import AddOrUpdate from './goods-add-or-update'
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
    //查看商品富文本
    showFwb(html, name) {
      this.$alert(html ? html : '(无富文本内容)', name, {
        dangerouslyUseHTMLString: true
      });
    },
    //是否上架
    putaway(statu, id, version) {
      this.$http({
        url: this.$http.adornUrl('/sys/goods/update'),
        method: 'post',
        data: this.$http.adornData({
          'goodStatu': statu,
          'goodId': id,
          'version': version,
        })
      }).then(({
        data
      }) => {
        this.getDataList()
        console.log(data, '修改上架');
      })
    },
    //是否首页展示
    indexDisplay(display, id, version) {
      this.$http({
        url: this.$http.adornUrl('/sys/goods/update'),
        method: 'post',
        data: this.$http.adornData({
          'display': display,
          'goodId': id,
          'version': version,
        })
      }).then(({
        data
      }) => {
        this.getDataList()
        console.log(data, '修改上架');
      })
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/goods/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          'key': this.dataForm.key
        })
      }).then(({
        data
      }) => {
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
        return item.goodId
      })
      this.$confirm(`确定对[id=${ids.join(',')}]的商品进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/sys/goods/delete'),
          method: 'post',
          data: this.$http.adornData(ids, false)
        }).then(({
          data
        }) => {
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
