<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="车牌号" prop="carId">
        <el-input v-model="dataForm.carId" placeholder="车牌号"></el-input>
      </el-form-item>
      <el-form-item label="出险时间" prop="cxTime">
        <el-input v-model="dataForm.cxTime" placeholder="出险时间"></el-input>
      </el-form-item>
      <el-form-item label="出险地点" prop="cxPlace">
        <el-input v-model="dataForm.cxPlace" placeholder="出险地点"></el-input>
      </el-form-item>
      <el-form-item label="受损明细" prop="damageDetail">
        <el-input v-model="dataForm.damageDetail" placeholder="受损明细"></el-input>
      </el-form-item>
      <el-form-item label="事故现场相片" prop="damagePicture">
        <el-input v-model="dataForm.damagePicture" placeholder="事故现场相片"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        id: 0,
        carId: '',
        cxTime: '',
        cxPlace: '',
        damageDetail: '',
        damagePicture: ''
      },
      dataRule: {
        carId: [
          { required: true, message: '车牌号不能为空', trigger: 'blur' }
        ],
        cxTime: [
          { required: true, message: '出险时间不能为空', trigger: 'blur' }
        ],
        cxPlace: [
          { required: true, message: '出险地点不能为空', trigger: 'blur' }
        ],
        damageDetail: [
          { required: true, message: '受损明细不能为空', trigger: 'blur' }
        ],
        damagePicture: [
          { required: true, message: '事故现场相片不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/carcx/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.carId = data.carCx.carId
              this.dataForm.cxTime = data.carCx.cxTime
              this.dataForm.cxPlace = data.carCx.cxPlace
              this.dataForm.damageDetail = data.carCx.damageDetail
              this.dataForm.damagePicture = data.carCx.damagePicture
            }
          })
        }
      })
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/sys/carcx/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'carId': this.dataForm.carId,
              'cxTime': this.dataForm.cxTime,
              'cxPlace': this.dataForm.cxPlace,
              'damageDetail': this.dataForm.damageDetail,
              'damagePicture': this.dataForm.damagePicture
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.visible = false
                  this.$emit('refreshDataList')
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }
      })
    }
  }
}
</script>
