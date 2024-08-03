<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="补考学员ID" prop="stuId">
      <el-input v-model="dataForm.stuId" placeholder="补考学员ID"></el-input>
    </el-form-item>
    <el-form-item label="补考学员姓名" prop="stuName">
      <el-input v-model="dataForm.stuName" placeholder="补考学员姓名"></el-input>
    </el-form-item>
    <el-form-item label="补考科目" prop="subject">
      <el-input v-model="dataForm.subject" placeholder="补考科目"></el-input>
    </el-form-item>
    <el-form-item label="补考费用" prop="money">
      <el-input v-model="dataForm.money" placeholder="补考费用"></el-input>
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
          stuId: '',
          stuName: '',
          subject: '',
          money: ''
        },
        dataRule: {
          stuId: [
            { required: true, message: '补考学员ID不能为空', trigger: 'blur' }
          ],
          stuName: [
            { required: true, message: '补考学员姓名不能为空', trigger: 'blur' }
          ],
          subject: [
            { required: true, message: '补考科目不能为空', trigger: 'blur' }
          ],
          money: [
            { required: true, message: '补考费用不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/bukao/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.stuId = data.bukao.stuId
                this.dataForm.stuName = data.bukao.stuName
                this.dataForm.subject = data.bukao.subject
                this.dataForm.money = data.bukao.money
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
              url: this.$http.adornUrl(`/app/bukao/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'stuId': this.dataForm.stuId,
                'stuName': this.dataForm.stuName,
                'subject': this.dataForm.subject,
                'money': this.dataForm.money
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
