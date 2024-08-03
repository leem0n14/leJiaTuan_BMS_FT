<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="车牌号" prop="carId">
        <el-input v-model="dataForm.carId" placeholder="车牌号"></el-input>
      </el-form-item>
      <el-form-item label="维修商家名称" prop="factory">
        <el-input v-model="dataForm.factory" placeholder="维修商家名称"></el-input>
      </el-form-item>
      <el-form-item label="维修内容" prop="wxBody">
        <el-input v-model="dataForm.wxBody" placeholder="维修内容"></el-input>
      </el-form-item>
      <el-form-item label="维修金额" prop="wxPrice">
        <el-input v-model="dataForm.wxPrice" placeholder="维修金额"></el-input>
      </el-form-item>
      <el-form-item label="维修日期" prop="wxTime">
        <el-input v-model="dataForm.wxTime" placeholder="维修日期"></el-input>
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
        factory: '',
        wxBody: '',
        wxPrice: '',
        wxTime: ''
      },
      dataRule: {
        carId: [
          { required: true, message: '车牌号不能为空', trigger: 'blur' }
        ],
        factory: [
          { required: true, message: '维修商家名称不能为空', trigger: 'blur' }
        ],
        wxBody: [
          { required: true, message: '维修内容不能为空', trigger: 'blur' }
        ],
        wxPrice: [
          { required: true, message: '维修金额不能为空', trigger: 'blur' }
        ],
        wxTime: [
          { required: true, message: '维修日期不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/sys/carwx/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.carId = data.carWx.carId
              this.dataForm.factory = data.carWx.factory
              this.dataForm.wxBody = data.carWx.wxBody
              this.dataForm.wxPrice = data.carWx.wxPrice
              this.dataForm.wxTime = data.carWx.wxTime
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
            url: this.$http.adornUrl(`/sys/carwx/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'carId': this.dataForm.carId,
              'factory': this.dataForm.factory,
              'wxBody': this.dataForm.wxBody,
              'wxPrice': this.dataForm.wxPrice,
              'wxTime': this.dataForm.wxTime
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
