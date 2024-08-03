<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="学员姓名" prop="stuName">
      <el-input v-model="dataForm.stuName" placeholder="学员姓名"></el-input>
    </el-form-item>
    <el-form-item label="电话号码" prop="phone">
      <el-input v-model="dataForm.phone" placeholder="电话号码"></el-input>
    </el-form-item>
    <el-form-item label="预约科目" prop="subjects">
      <el-input v-model="dataForm.subjects" placeholder="预约科目"></el-input>
    </el-form-item>
    <el-form-item label="考试时间" prop="examTime">
      <el-input v-model="dataForm.examTime" placeholder="考试时间"></el-input>
    </el-form-item>
    <el-form-item label="考场地址" prop="site">
      <el-input v-model="dataForm.site" placeholder="考场地址"></el-input>
    </el-form-item>
    <el-form-item label="考试分数" prop="grade">
      <el-input v-model="dataForm.grade" placeholder="考试分数"></el-input>
    </el-form-item>
    <el-form-item label="是否补考" prop="reExam">
      <el-input v-model="dataForm.reExam" placeholder="是否补考"></el-input>
    </el-form-item>
    <el-form-item label="状态{0 已预约 1通过 2 未通过 3错过}" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态{0 已预约 1通过 2 未通过 3错过}"></el-input>
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
          userId: '',
          stuName: '',
          phone: '',
          subjects: '',
          examTime: '',
          site: '',
          grade: '',
          reExam: '',
          status: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          stuName: [
            { required: true, message: '学员姓名不能为空', trigger: 'blur' }
          ],
          phone: [
            { required: true, message: '电话号码不能为空', trigger: 'blur' }
          ],
          subjects: [
            { required: true, message: '预约科目不能为空', trigger: 'blur' }
          ],
          examTime: [
            { required: true, message: '考试时间不能为空', trigger: 'blur' }
          ],
          site: [
            { required: true, message: '考场地址不能为空', trigger: 'blur' }
          ],
          grade: [
            { required: true, message: '考试分数不能为空', trigger: 'blur' }
          ],
          reExam: [
            { required: true, message: '是否补考不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态{0 已预约 1通过 2 未通过 3错过}不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/sys/mpexam/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.mpExam.userId
                this.dataForm.stuName = data.mpExam.stuName
                this.dataForm.phone = data.mpExam.phone
                this.dataForm.subjects = data.mpExam.subjects
                this.dataForm.examTime = data.mpExam.examTime
                this.dataForm.site = data.mpExam.site
                this.dataForm.grade = data.mpExam.grade
                this.dataForm.reExam = data.mpExam.reExam
                this.dataForm.status = data.mpExam.status
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
              url: this.$http.adornUrl(`/sys/mpexam/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'stuName': this.dataForm.stuName,
                'phone': this.dataForm.phone,
                'subjects': this.dataForm.subjects,
                'examTime': this.dataForm.examTime,
                'site': this.dataForm.site,
                'grade': this.dataForm.grade,
                'reExam': this.dataForm.reExam,
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
