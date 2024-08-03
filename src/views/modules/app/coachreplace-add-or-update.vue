<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="需要更换的教练ID" prop="coachId">
      <el-input v-model="dataForm.coachId" placeholder="需要更换的教练ID"></el-input>
    </el-form-item>
    <el-form-item label="" prop="coachName">
      <el-input v-model="dataForm.coachName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="更换教练理由" prop="message">
      <el-input v-model="dataForm.message" placeholder="更换教练理由"></el-input>
    </el-form-item>
    <el-form-item label="审核状态{0 审核中 1审核成功 2审核失败}" prop="statu">
      <el-input v-model="dataForm.statu" placeholder="审核状态{0 审核中 1审核成功 2审核失败}"></el-input>
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
          coachId: '',
          coachName: '',
          message: '',
          statu: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          coachId: [
            { required: true, message: '需要更换的教练ID不能为空', trigger: 'blur' }
          ],
          coachName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          message: [
            { required: true, message: '更换教练理由不能为空', trigger: 'blur' }
          ],
          statu: [
            { required: true, message: '审核状态{0 审核中 1审核成功 2审核失败}不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/coachreplace/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.coachReplace.userId
                this.dataForm.coachId = data.coachReplace.coachId
                this.dataForm.coachName = data.coachReplace.coachName
                this.dataForm.message = data.coachReplace.message
                this.dataForm.statu = data.coachReplace.statu
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
              url: this.$http.adornUrl(`/app/coachreplace/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'coachId': this.dataForm.coachId,
                'coachName': this.dataForm.coachName,
                'message': this.dataForm.message,
                'statu': this.dataForm.statu
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
