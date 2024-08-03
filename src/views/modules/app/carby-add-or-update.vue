<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="车牌号" prop="carId">
        <el-input v-model="dataForm.carId" placeholder="车牌号"></el-input>
      </el-form-item>
      <el-form-item label="保养日期" prop="time">
        <el-input v-model="dataForm.time" placeholder="保养日期"></el-input>
      </el-form-item>
      <el-form-item label="行驶里程" prop="mile">
        <el-input v-model="dataForm.mile" placeholder="行驶里程"></el-input>
      </el-form-item>
      <el-form-item label="保养金额" prop="price">
        <el-input v-model="dataForm.price" placeholder="保养金额"></el-input>
      </el-form-item>
      <el-form-item label="保养项目" prop="projects">
        <el-input v-model="dataForm.projects" placeholder="保养项目"></el-input>
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
        time: '',
        mile: '',
        price: '',
        projects: ''
      },
      dataRule: {
        carId: [
          { required: true, message: '车牌号不能为空', trigger: 'blur' }
        ],
        time: [
          { required: true, message: '保养日期不能为空', trigger: 'blur' }
        ],
        mile: [
          { required: true, message: '行驶里程不能为空', trigger: 'blur' }
        ],
        price: [
          { required: true, message: '保养金额不能为空', trigger: 'blur' }
        ],
        projects: [
          { required: true, message: '保养项目不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/sys/carby/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.carId = data.carBy.carId
              this.dataForm.time = data.carBy.time
              this.dataForm.mile = data.carBy.mile
              this.dataForm.price = data.carBy.price
              this.dataForm.projects = data.carBy.projects
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
            url: this.$http.adornUrl(`/sys/carby/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'carId': this.dataForm.carId,
              'time': this.dataForm.time,
              'mile': this.dataForm.mile,
              'price': this.dataForm.price,
              'projects': this.dataForm.projects
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
