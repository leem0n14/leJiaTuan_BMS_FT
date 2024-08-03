<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <!-- <el-form-item>
        <el-input style="width: 160px;" v-model="dataForm.key" placeholder="手机号" clearable></el-input>
      </el-form-item> -->
      <el-form-item>
        <el-input style="width: 160px;" v-model="dataForm.key1" placeholder="姓名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input style="width: 160px;" v-model="dataForm.key4" placeholder="城市" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-select v-model="dataForm.key2" clearable placeholder="是否报考驾校">
          <el-option v-for="item in ['已报名驾校', '未报名驾校']" :key="item" :label="item" :value="item">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-date-picker v-model="dataForm.key3" type="date" placeholder="选择时间" value-format="yyyy-MM-dd"
          format="yyyy-MM-dd">
        </el-date-picker>
        <!-- <el-input style="width: 160px;" v-model="dataForm.key2" placeholder="城市" clearable></el-input> -->
      </el-form-item>
      <!-- <el-form-item>
        <el-select v-model="dataForm.key" clearable placeholder="考驾照的目的">
          <el-option v-for="item in ['有车没本', '计划买车', '纯为拿本']" :key="item" :label="item" :value="item">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-select v-model="dataForm.key" clearable placeholder="报考类型">
          <el-option v-for="item in ['小车', '货车', '客车', '摩托车']" :key="item" :label="item" :value="item">
          </el-option>
        </el-select>
      </el-form-item> -->
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <!-- <el-button v-if="isAuth('app:user:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <!-- <el-button v-if="isAuth('app:user:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button> -->
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="userId" header-align="center" align="center" label="用户id" width="70">
      </el-table-column>
      <el-table-column prop="phone" header-align="center" align="center" label="电话" width="110">
      </el-table-column>
      <el-table-column prop="name" header-align="center" align="center" label="姓名">
      </el-table-column>
      <el-table-column prop="head" header-align="center" align="center" label="微信头像">
        <template slot-scope="scope">
          <div v-if="scope.row.head !== null">
            <img :src="`${cdn}/super/user/${scope.row.head}`" alt="头像" style="width: 70px;">
          </div>
          <div v-else><el-tag>暂无头像</el-tag></div>
        </template>
      </el-table-column>
      <el-table-column prop="sex" header-align="center" align="center" label="性别">
        <template slot-scope="scope">
          <div>
            <el-tag type="success" v-if="scope.row.sex === '男士'">{{ '男士' }}</el-tag>
            <el-tag type="danger" v-else-if="scope.row.sex === '女士'">{{ '女士' }}</el-tag>
            <!-- <el-tag v-else>{{ scope.row.sex }}</el-tag> -->
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="address" header-align="center" align="center" label="城市">
      </el-table-column>
      <el-table-column prop="statu" header-align="center" align="center" label="是否报考驾校" width="110">
        <template slot-scope="scope">
          <el-tag type="info" v-if="scope.row.statu !== null">{{ scope.row.statu }}</el-tag>
          <!-- <el-tag v-else>{{ scope.row.statu }}</el-tag> -->
        </template>
      </el-table-column>
      <el-table-column prop="driverType" header-align="center" align="center" label="报考驾驶证类型">
        <template slot-scope="scope">
          <el-tag type="info" v-if="scope.row.driverType !== null">{{ scope.row.driverType }}</el-tag>
          <!-- <el-tag v-else>{{ scope.row.statu }}</el-tag> -->
        </template>
      </el-table-column>
      <el-table-column prop="goal" header-align="center" align="center" label="考驾照目的">
        <template slot-scope="scope">
          <el-tag type="info" v-if="scope.row.goal !== null">{{ scope.row.goal }}</el-tag>
          <!-- <el-tag v-else>{{ scope.row.statu }}</el-tag> -->
        </template>
      </el-table-column>
      <el-table-column prop="point" header-align="center" align="center" label="积分">
      </el-table-column>
      <el-table-column prop="money" header-align="center" align="center" label="佣金">
      </el-table-column>
      <el-table-column prop="identity" header-align="center" align="center" label="身份">
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.identity === 0">{{ '普通用户' }}</el-tag>
            <el-tag v-else-if="scope.row.identity === 1" type="success">{{ '学员' }}</el-tag>
            <el-tag v-else-if="scope.row.identity === 2" type="info">{{ '教练' }}</el-tag>
            <el-tag type="danger" v-else>{{ '参数错误' }}</el-tag>
          </div>
        </template>
      </el-table-column>
      <!-- <el-table-column prop="parentId" header-align="center" align="center" label="父id">
      </el-table-column> -->
      <el-table-column prop="createTime" header-align="center" align="center" label="创建时间">
      </el-table-column>
      <!-- <el-table-column prop="questionNum1" header-align="center" align="center" label="科目一练题">
      </el-table-column>
      <el-table-column prop="questionNum4" header-align="center" align="center" label="科目四练题">
      </el-table-column> -->
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.userId)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.userId)">删除</el-button>
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
import AddOrUpdate from './user-add-or-update'
export default {
  data() {
    return {
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan',
      dataForm: {
        key1: '',//姓名
        key2: '',//是否报名
        key3: '',//时间
        key4: '',//城市
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
        url: this.$http.adornUrl('/sys/wxuser/list'),
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
        return item.userId
      })
      this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/sys/wxuser/delete'),
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
