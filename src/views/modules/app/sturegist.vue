<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="学生姓名 驾校名 商品名" clearable @change="getDataList()"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <!-- <el-button v-if="isAuth('sys:sturegist:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <el-button v-if="isAuth('sys:sturegist:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="stuName" header-align="center" align="center" label="姓名">
      </el-table-column>
      <el-table-column prop="stuPhone" header-align="center" align="center" label="电话">
      </el-table-column>
      <el-table-column prop="schoolName" header-align="center" align="center" label="报名驾校">
      </el-table-column>
      <el-table-column prop="stuSex" header-align="center" align="center" label="性别">
      </el-table-column>
      <el-table-column prop="classType" header-align="center" align="center" label="报班类型">
      </el-table-column>
      <el-table-column prop="statu" header-align="center" align="center" label="是否报名成功">
      </el-table-column>
      <el-table-column prop="noReason" header-align="center" align="center" label="不通过原因">
      </el-table-column>
      <el-table-column prop="cardId" header-align="center" align="center" label="身份证号">
      </el-table-column>
      <el-table-column prop="cardFront" header-align="center" align="center" label="身份证正面照">
        <template slot-scope="scope">
          <!-- <a :href="cdn + scope.row.cardFront" target="_blank" rel="noopener noreferrer">{{ scope.row.cardFront }}</a> -->
          <img :src="cdn + scope.row.cardFront" @click="showImg(scope.row.cardFront)">
        </template>
      </el-table-column>
      <el-table-column prop="cardBack" header-align="center" align="center" label="身份证背面照">
        <template slot-scope="scope">
          <!-- <a :href="cdn + scope.row.cardBack" target="_blank" rel="noopener noreferrer">{{ scope.row.cardBack }}</a> -->
          <img :src="cdn + scope.row.cardBack" @click="showImg(scope.row.cardBack)">
        </template>
      </el-table-column>
      <el-table-column prop="photo" header-align="center" align="center" label="上半身照">
        <template slot-scope="scope">
          <!-- <a :href="cdn + scope.row.photo" target="_blank" rel="noopener noreferrer">{{ scope.row.photo }}</a> -->
          <img :src="cdn + scope.row.photo" @click="showImg(scope.row.photo)">
        </template>
      </el-table-column>
      <el-table-column prop="tijianbiao" header-align="center" align="center" label="体检表照片">
        <template slot-scope="scope">
          <!-- <a :href="cdn + scope.row.tijianbiao" target="_blank" rel="noopener noreferrer">{{ scope.row.tijianbiao }}</a> -->
          <img :src="cdn + scope.row.tijianbiao" @click="showImg(scope.row.tijianbiao)">
        </template>
      </el-table-column>
      <el-table-column prop="goodName" header-align="center" align="center" label="报名班型">
      </el-table-column>
      <el-table-column prop="addTime" header-align="center" align="center" label="报名时间">
      </el-table-column>
      <el-table-column prop="cardAd" header-align="center" align="center" label="身份证地址">
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <div v-if="scope.row.statu === 0">
            <el-button-group>
              <el-button type="success" size="mini" @click="audit(1, scope.row.id)">同意</el-button>
              <el-button type="danger" size="mini" @click="audit(2, scope.row.id)">拒绝</el-button>
            </el-button-group>
          </div>
          <div v-else>
            <el-tag type="success" v-if="scope.row.statu === 1">{{ '已同意' }}</el-tag>
            <el-tag type="danger" v-else-if="scope.row.statu === 2">{{ '已拒绝' }}</el-tag>
          </div>
          <!-- <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button> -->
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>

    <el-dialog title="请输入拒绝原因" :visible.sync="dialogVisible" width="30%">
      <el-input v-model="noReason" placeholder="请输入拒绝原因"></el-input>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="subAudit(2, id)">确 定</el-button>
      </span>
    </el-dialog>

    <el-dialog :visible.sync="imgWin" width="50%" :before-close="handleClose">
      <img :src="imgUrl ? (cdn + imgUrl) : ''">
    </el-dialog>

    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from './sturegist-add-or-update'
export default {
  data() {
    return {
      dataForm: {
        key: ''
      },
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/student/',
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      imgWin: false,
      imgUrl: '',
      addOrUpdateVisible: false,
      noReason: null,
      id: null,
      dialogVisible: false,
    }
  },
  components: {
    AddOrUpdate
  },
  activated() {
    this.getDataList()
  },
  methods: {
    showImg(url) {
      this.imgUrl = url
      this.imgWin = true
    },
    handleClose() {
      this.imgUrl = ''
      this.imgWin = false
    },
    subAudit(statu, id) {
      this.$http({
        url: this.$http.adornUrl(`/sys/sturegist/update`),
        method: 'post',
        data: this.$http.adornData({
          statu,
          id,
          noReason: this.noReason,
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dialogVisible = false
          // console.log(data);
          this.getDataList()
          this.$message({
            type: 'success',
            message: '操作成功'
          })
        } else {
          if (data.msg)
            this.$message(data.msg)
          else {
            this.$message('未知错误')
          }
        }
      })
    },

    //审核
    audit(statu, id) {
      this.id = id
      // let noReason = null
      let char
      if (statu == 2) {
        char = '拒绝'
      } else if (statu == 1) {
        char = '同意'
      }
      this.$confirm(`你确定要进行${char}操作吗`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        if (statu == 2) {
          this.dialogVisible = true
        } else if (statu == 1) {
          this.subAudit(statu, id)
        }
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消'
        });
      })

    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/sturegist/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          'key': this.dataForm.key != '' ? this.dataForm.key : null
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
          url: this.$http.adornUrl('/sys/sturegist/delete'),
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
img {
  width: 100%;
  height: 100%;
}
</style>
