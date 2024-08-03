<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
      label-width="80px">
      <el-form-item label="公告类型" prop="type">
        <el-input v-model="dataForm.type" placeholder="公告类型"></el-input>
      </el-form-item>
      <el-form-item label="公告" prop="announcement">
        <el-input v-model="dataForm.announcement" placeholder="公告"></el-input>
      </el-form-item>
      <el-form-item label="状态" prop="statu">
        <!-- <el-input v-model="dataForm.statu" placeholder="状态{0关闭 1开启}"></el-input> -->
        <el-radio v-model="dataForm.statu" label="0">关闭</el-radio>
        <el-radio v-model="dataForm.statu" label="1">开启</el-radio>
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
        type: '',
        announcement: '',
        statu: ''
      },
      dataRule: {
        type: [
          { required: true, message: '公告类型不能为空', trigger: 'blur' }
        ],
        announcement: [
          { required: true, message: '公告不能为空', trigger: 'blur' }
        ],
        statu: [
          { required: true, message: '状态不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/app/announcement/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.type = data.announcement.type
              this.dataForm.announcement = data.announcement.announcement
              this.dataForm.statu = data.announcement.statu
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
            url: this.$http.adornUrl(`/sys/announcement/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'type': this.dataForm.type,
              'announcement': this.dataForm.announcement,
              'statu': this.dataForm.statu
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
