<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="跟换教练id" prop="coachId">
      <el-input v-model="dataForm.coachId" placeholder="跟换教练id"></el-input>
    </el-form-item>
    <el-form-item label="跟换教练理由" prop="message">
      <el-input v-model="dataForm.message" placeholder="跟换教练理由"></el-input>
    </el-form-item>
    <el-form-item label="更换的教练id" prop="reCoachid">
      <el-input v-model="dataForm.reCoachid" placeholder="更换的教练id"></el-input>
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
          message: '',
          reCoachid: '',
          statu: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          coachId: [
            { required: true, message: '跟换教练id不能为空', trigger: 'blur' }
          ],
          message: [
            { required: true, message: '跟换教练理由不能为空', trigger: 'blur' }
          ],
          reCoachid: [
            { required: true, message: '更换的教练id不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/recoach/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.reCoach.userId
                this.dataForm.coachId = data.reCoach.coachId
                this.dataForm.message = data.reCoach.message
                this.dataForm.reCoachid = data.reCoach.reCoachid
                this.dataForm.statu = data.reCoach.statu
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
              url: this.$http.adornUrl(`/app/recoach/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'coachId': this.dataForm.coachId,
                'message': this.dataForm.message,
                'reCoachid': this.dataForm.reCoachid,
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
