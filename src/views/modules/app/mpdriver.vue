<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <div style="display:flex;">
          <el-input style="margin:0 5px" v-model="dataForm.stuNameKey" placeholder="学员名" clearable></el-input>
          <el-input style="margin:0 5px" v-model="dataForm.coachNameKey" placeholder="教练名" clearable></el-input>
          <el-input style="margin:0 5px" v-model="dataForm.siteKey" placeholder="场地" clearable></el-input>
          <!-- <el-input style="margin:0 5px" v-model="dataForm.timeKey" placeholder="日期" clearable></el-input> -->
          <el-input style="margin:0 5px" v-model="dataForm.typeKey" placeholder="练车类型" clearable></el-input>
          <div style="width: 150px;margin:0 5px">
            <el-date-picker v-model="dataForm.timeKey" type="date" placeholder="选择日期" value-format="yyyy-MM-dd">
            </el-date-picker>
          </div>
        </div>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()" style="margin: 0 0px 0 60px">查询</el-button>
        <!-- <el-button v-if="isAuth('app:mpdriver:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <el-button v-if="isAuth('app:mpdriver:delete')" type="danger" @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0">批量删除</el-button>
        <el-button-group>
          <el-button type="success" :disabled="dataListSelections.length <= 0" @click="accept()">批量同意</el-button>
          <!-- <el-button type="warning" :disabled="dataListSelections.length <= 0" @click="unAccept()">批量拒绝</el-button> -->
        </el-button-group>
        <el-button type="warning" @click="showExcel = true" :disabled="dataListSelections.length <= 0">导出</el-button>
      </el-form-item>

    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50">
      </el-table-column>
      <el-table-column prop="mpName" header-align="center" align="center" label="姓名" width="120">
      </el-table-column>
      <el-table-column prop="coachName" header-align="center" align="center" label="教练" width="120">
      </el-table-column>
      <el-table-column prop="school" header-align="center" align="center" label="驾校" width="120">
      </el-table-column>
      <el-table-column prop="phone" header-align="center" align="center" label="电话" width="120">
      </el-table-column>
      <el-table-column prop="carTypes" header-align="center" align="center" label="练车类型" width="120">
      </el-table-column>
      <el-table-column prop="carId" header-align="center" align="center" label="分配到的车辆" width="120">
        <template slot-scope="scope">
          <div>{{ scope.row.carId ? scope.row.carId : '未分配' }}</div>
        </template>
      </el-table-column>
      <el-table-column prop="addName" header-align="center" align="center" label="接送地名" width="120">
      </el-table-column>
      <el-table-column prop="mpAdd" header-align="center" align="center" label="接送地址" width="120">
      </el-table-column>
      <el-table-column prop="site" header-align="center" align="center" label="练车场地" width="120">
      </el-table-column>
      <el-table-column prop="object" header-align="center" align="center" label="科目名称" width="120">
      </el-table-column>
      <el-table-column prop="addTime" header-align="center" align="center" label="预约日期" width="120">
      </el-table-column>
      <el-table-column prop="carTime" header-align="center" align="center" label="练车时间段" width="120">
      </el-table-column>
      <el-table-column prop="jiesongStatu" header-align="center" align="center" label="接送" width="50">
        <template slot-scope="scope">
          <div>{{ scope.row.jiesongStatu == 0 ? '否' : '是' }}</div>
        </template>
      </el-table-column>
      <el-table-column prop="statu" header-align="center" align="center" label="预约是否通过" width="120">
        <template slot-scope="scope">
          <el-tag :type="scope.row.statu == '已完成' ? 'success' : 'danger'" v-if="scope.row.statu != '审核中'">{{
            scope.row.statu
          }}</el-tag>
          <div v-else>{{ scope.row.statu }}</div>
        </template>
      </el-table-column>

      <el-table-column prop="evaluateText" header-align="center" align="center" label="学员评价" width="200">
        <template slot-scope="scope">
          <div>{{ scope.row.evaluateText ? scope.row.evaluateText : '(暂无学员评价)' }}</div>
        </template>
      </el-table-column>
      <el-table-column prop="reason" header-align="center" align="center" label="不同意原因" width="200">
        <template slot-scope="scope">
          <div>{{ scope.row.reason ? scope.row.reason : '(无)' }}</div>
        </template>
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <div v-if="scope.row.statu === '审核中'">
            <el-button-group>
              <!-- <el-button type="success" size="mini" @click="update(scope.row.id)">同意</el-button> -->
              <el-button type="danger" size="mini" @click="reject(scope.row.id)">拒绝</el-button>
            </el-button-group>
          </div>
          <!-- <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button> -->
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100, 200]" :page-size="pageSize" :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>

    <el-dialog title="拒绝原因" :visible.sync="dialogVisible" width="30%" :before-close="handleClose">
      <el-input type="textarea" :rows="2" placeholder="请输入拒绝原因" v-model="reson">
      </el-input>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="reject2(id)">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog title="导出excel" :visible.sync="showExcel" width="50%">

      <el-input v-model="fileName" placeholder="输入导出的excel文件名"
        style="display: inline-block;margin-bottom: 5px;"></el-input>
      <el-input v-model="title" placeholder="输入导出的excel表格标题" style="display: inline-block;margin-bottom: 5px;"></el-input>
      <div style="display: flex;flex-direction: column;align-items: flex-end;">
        <download-excel class="export-excel-wrapper" type="xls" :data="DetailsForm" :fields="json_fields" :header="title"
          :name="title" :before-generate="generate" :before-finish="finish">
          <el-button style="margin-top: 30px;" type="success" v-if="fileName != '' && !isExcel">导出</el-button>
          <el-tag type="success" v-else>选择要导出的数据和输入标题后显示导出按钮</el-tag>
        </download-excel>
      </div>
      <!-- <span slot="footer" class="dialog-footer">
        <el-button @click="showExcel = false">取 消</el-button>
        <el-button type="primary" @click="showExcel = false">确 定</el-button>
      </span> -->
    </el-dialog>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from './mpdriver-add-or-update'
export default {
  data() {
    return {
      dataForm: {
        // key: '',
        stuNameKey: null,
        coachNameKey: null,
        siteKey: null,
        timeKey: null,
        typeKey: null,
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      fileName: 'mp.xlsx',
      title: "预约练车",
      showExcel: false,
      json_fields: {
        "姓名": 'mpName',
        "教练ID": 'coachId',
        "教练": 'coachName',
        "驾校": "school",
        "电话": 'phone',
        "练车类型": 'carTypes',
        "场地": 'site',
        "接送地址": 'mpAdd',
        "科目名称": 'object',
        '练车日期': 'addTime',
        "练车时间段": 'carTime',
        "是否需要接送": {
          field: 'jiesongStatu',
          callback: (v) => {
            if (v == 0) {
              return '否'
            } else if (v == 1) {
              return '是'
            }
          }
        },
        "预约是否通过": 'statu',
        "不同意原因": 'reason',
      },
      DetailsForm: [],
      dialogVisible: false,
      reson: null,
      id: null,
    }
  },
  components: {
    AddOrUpdate
  },
  activated() {
    this.getDataList()
  },
  computed: {
    isExcel() {
      if (this.DetailsForm.length == 0) {
        return true
      } else {
        return false
      }
    }
  },
  methods: {
    //excel下载进度
    finish() {
      // console.log(e);
      // this.$message('下载完成')
    },
    generate() {
      // console.log(e);
      this.$message('开始下载')
    },
    //批量拒绝
    unAccept(id) {
      var ids = id ? [id] : this.dataListSelections.map(item => {
        return item.id
      })
      this.$http({
        url: this.$http.adornUrl(`/sys/mpdriver/updateByBatch`),
        method: 'post',
        data: this.$http.adornData({
          ids,
          statu: '拒绝',
        })
      }).then((data) => {
        console.log(data);
        if (data.code == 0) {
          this.$message(data.msg)

        } else {
          this.$message.error(data.msg)
        }
        this.getDataList()
      })
    },
    //批量同意
    accept(id) {
      var ids = id ? [id] : this.dataListSelections.map(item => {
        return item.id
      })
      this.$http({
        url: this.$http.adornUrl(`/sys/mpdriver/updateByBatch`),
        method: 'post',
        data: this.$http.adornData({
          ids,
          statu: '同意',
        })
      }).then((data) => {
        console.log(data);
        if (data.code == 0) {
          this.$message(data.msg)

        } else {
          this.$message.error(data.msg)
        }
        this.getDataList()
      })
    },

    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done();
        })
        .catch(_ => { });
    },
    //同意或拒绝
    update(id) {
      var char = '同意'
      this.$http({
        url: this.$http.adornUrl(`/sys/mpdriver/update`),
        method: 'post',
        data: this.$http.adornData({
          id,
          statu: char,
        })
      }).then(({ data }) => {
        console.log(data);
        if (data.code == 0) {

        } else {

        }
        this.getDataList()
      })


    },
    //发送原因
    reject2(id) {
      var char = '拒绝'
      this.$http({
        url: this.$http.adornUrl(`/sys/mpdriver/update`),
        method: 'post',
        data: this.$http.adornData({
          id,
          statu: char,
          reason: this.reson
        })
      }).then(({ data }) => {
        console.log(data);
        if (data.code == 0) {
          this.dialogVisible = false
          this.id = null
          this.reson = null

        } else {

        }

        this.getDataList()
      })
    },
    // 拒绝原因
    reject(id) {
      this.dialogVisible = true
      this.id = id
    },

    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/mpdriver/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          // 'key': this.dataForm.key,

          'key1': this.dataForm.stuNameKey == '' ? null : this.dataForm.stuNameKey,
          'key2': this.dataForm.coachNameKey == '' ? null : this.dataForm.coachNameKey,
          'key3': this.dataForm.siteKey == '' ? null : this.dataForm.siteKey,
          'key4': this.dataForm.timeKey == '' ? null : this.dataForm.timeKey,
          'key5': this.dataForm.typeKey == '' ? null : this.dataForm.typeKey,
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
      // console.log('duox', val);
      this.DetailsForm = val
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
          url: this.$http.adornUrl('/sys/mpdriver/delete'),
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
