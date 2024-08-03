<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="车辆编号" prop="carId">
      <el-input v-model="dataForm.carId" placeholder="车辆编号"></el-input>
    </el-form-item>
    <el-form-item label="车辆类型" prop="carType">
      <el-input v-model="dataForm.carType" placeholder="车辆类型"></el-input>
    </el-form-item>
    <el-form-item label="学生id" prop="stuId">
      <el-input v-model="dataForm.stuId" placeholder="学生id"></el-input>
    </el-form-item>
    <el-form-item label="学生姓名" prop="stuName">
      <el-input v-model="dataForm.stuName" placeholder="学生姓名"></el-input>
    </el-form-item>
    <el-form-item label="分车时间" prop="time">
      <el-input v-model="dataForm.time" placeholder="分车时间"></el-input>
    </el-form-item>
    <el-form-item label="完成时间" prop="finishTime">
      <el-input v-model="dataForm.finishTime" placeholder="完成时间"></el-input>
    </el-form-item>
    <el-form-item label="状态{0 练车中 1练车完成}" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态{0 练车中 1练车完成}"></el-input>
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
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          carId: '',
          carType: '',
          stuId: '',
          stuName: '',
          time: '',
          finishTime: '',
          status: ''
        },
        dataRule: {
          carId: [
            { required: true, message: '车辆编号不能为空', trigger: 'blur' }
          ],
          carType: [
            { required: true, message: '车辆类型不能为空', trigger: 'blur' }
          ],
          stuId: [
            { required: true, message: '学生id不能为空', trigger: 'blur' }
          ],
          stuName: [
            { required: true, message: '学生姓名不能为空', trigger: 'blur' }
          ],
          time: [
            { required: true, message: '分车时间不能为空', trigger: 'blur' }
          ],
          finishTime: [
            { required: true, message: '完成时间不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态{0 练车中 1练车完成}不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sys/fenche/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.carId = data.fenche.carId
                this.dataForm.carType = data.fenche.carType
                this.dataForm.stuId = data.fenche.stuId
                this.dataForm.stuName = data.fenche.stuName
                this.dataForm.time = data.fenche.time
                this.dataForm.finishTime = data.fenche.finishTime
                this.dataForm.status = data.fenche.status
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/fenche/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'carId': this.dataForm.carId,
                'carType': this.dataForm.carType,
                'stuId': this.dataForm.stuId,
                'stuName': this.dataForm.stuName,
                'time': this.dataForm.time,
                'finishTime': this.dataForm.finishTime,
                'status': this.dataForm.status
              })
            }).then(({data}) => {
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
