<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="学员ID" prop="stuId">
      <el-input v-model="dataForm.stuId" placeholder="学员ID"></el-input>
    </el-form-item>
    <el-form-item label="学员姓名" prop="stuName">
      <el-input v-model="dataForm.stuName" placeholder="学员姓名"></el-input>
    </el-form-item>
    <el-form-item label="考试科目" prop="subject">
      <el-input v-model="dataForm.subject" placeholder="考试科目"></el-input>
    </el-form-item>
    <el-form-item label="考试时间" prop="examTime">
      <el-input v-model="dataForm.examTime" placeholder="考试时间"></el-input>
    </el-form-item>
    <el-form-item label="考试情况" prop="statu">
      <el-input v-model="dataForm.statu" placeholder="考试情况"></el-input>
    </el-form-item>
    <el-form-item label="考试分数" prop="score">
      <el-input v-model="dataForm.score" placeholder="考试分数"></el-input>
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
          examTime: '',
          statu: '',
          score: ''
        },
        dataRule: {
          stuId: [
            { required: true, message: '学员ID不能为空', trigger: 'blur' }
          ],
          stuName: [
            { required: true, message: '学员姓名不能为空', trigger: 'blur' }
          ],
          subject: [
            { required: true, message: '考试科目不能为空', trigger: 'blur' }
          ],
          examTime: [
            { required: true, message: '考试时间不能为空', trigger: 'blur' }
          ],
          statu: [
            { required: true, message: '考试情况不能为空', trigger: 'blur' }
          ],
          score: [
            { required: true, message: '考试分数不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/exam/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.stuId = data.exam.stuId
                this.dataForm.stuName = data.exam.stuName
                this.dataForm.subject = data.exam.subject
                this.dataForm.examTime = data.exam.examTime
                this.dataForm.statu = data.exam.statu
                this.dataForm.score = data.exam.score
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
              url: this.$http.adornUrl(`/app/exam/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'stuId': this.dataForm.stuId,
                'stuName': this.dataForm.stuName,
                'subject': this.dataForm.subject,
                'examTime': this.dataForm.examTime,
                'statu': this.dataForm.statu,
                'score': this.dataForm.score
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
