<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="" prop="carId">
        <el-input v-model="dataForm.carId" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="开始时间" prop="startTime">
        <el-input v-model="dataForm.startTime" placeholder="开始时间"></el-input>
      </el-form-item>
      <el-form-item label="到期时间" prop="endTime">
        <el-input v-model="dataForm.endTime" placeholder="到期时间"></el-input>
      </el-form-item>
      <el-form-item label="保险内容" prop="text">
        <el-input v-model="dataForm.text" placeholder="保险内容"></el-input>
      </el-form-item>
      <el-form-item label="保险公司" prop="company">
        <el-input v-model="dataForm.company" placeholder="保险公司"></el-input>
      </el-form-item>
      <el-form-item label="金额" prop="money">
        <el-input v-model="dataForm.money" placeholder="金额"></el-input>
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
        startTime: '',
        endTime: '',
        text: '',
        company: '',
        money: ''
      },
      dataRule: {
        carId: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        startTime: [
          { required: true, message: '开始时间不能为空', trigger: 'blur' }
        ],
        endTime: [
          { required: true, message: '到期时间不能为空', trigger: 'blur' }
        ],
        text: [
          { required: true, message: '保险内容不能为空', trigger: 'blur' }
        ],
        company: [
          { required: true, message: '保险公司不能为空', trigger: 'blur' }
        ],
        money: [
          { required: true, message: '金额不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/sys/carbx/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.carId = data.carBx.carId
              this.dataForm.startTime = data.carBx.startTime
              this.dataForm.endTime = data.carBx.endTime
              this.dataForm.text = data.carBx.text
              this.dataForm.company = data.carBx.company
              this.dataForm.money = data.carBx.money
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
            url: this.$http.adornUrl(`/sys/carbx/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'carId': this.dataForm.carId,
              'startTime': this.dataForm.startTime,
              'endTime': this.dataForm.endTime,
              'text': this.dataForm.text,
              'company': this.dataForm.company,
              'money': this.dataForm.money
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
