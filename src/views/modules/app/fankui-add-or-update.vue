<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
      label-width="80px">
      <el-form-item label="" prop="userId">
        <el-input v-model="dataForm.userId" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="反馈内容" prop="message">
        <el-input v-model="dataForm.message" placeholder="反馈内容"></el-input>
      </el-form-item>
      <!-- <el-form-item label="回复" prop="reply">
        <el-input v-model="dataForm.reply" placeholder="回复"></el-input>
      </el-form-item> -->
      <el-form-item label="反馈时间" prop="addtime">
        <el-input v-model="dataForm.addtime" placeholder="反馈时间"></el-input>
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
        userId: '',
        message: '',
        reply: '',
        addtime: ''
      },
      dataRule: {
        userId: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        message: [
          { required: true, message: '反馈内容不能为空', trigger: 'blur' }
        ],
        // reply: [
        //   { required: true, message: '回复不能为空', trigger: 'blur' }
        // ],
        addtime: [
          { required: true, message: '反馈时间不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/sys/fankui/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.userId = data.fankui.userId
              this.dataForm.message = data.fankui.message
              this.dataForm.reply = data.fankui.reply
              this.dataForm.addtime = data.fankui.addtime
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
            url: this.$http.adornUrl(`/sys/fankui/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'userId': this.dataForm.userId,
              'message': this.dataForm.message,
              'reply': this.dataForm.reply,
              'addtime': this.dataForm.addtime
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
