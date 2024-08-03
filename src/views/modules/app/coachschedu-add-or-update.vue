<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="教练id" prop="coachId">
      <el-input v-model="dataForm.coachId" placeholder="教练id"></el-input>
    </el-form-item>
    <el-form-item label="排班时间" prop="time">
      <el-input v-model="dataForm.time" placeholder="排班时间"></el-input>
    </el-form-item>
    <el-form-item label="场地id" prop="siteId">
      <el-input v-model="dataForm.siteId" placeholder="场地id"></el-input>
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
          coachId: '',
          time: '',
          siteId: ''
        },
        dataRule: {
          coachId: [
            { required: true, message: '教练id不能为空', trigger: 'blur' }
          ],
          time: [
            { required: true, message: '排班时间不能为空', trigger: 'blur' }
          ],
          siteId: [
            { required: true, message: '场地id不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/coachschedu/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.coachId = data.coachSchedu.coachId
                this.dataForm.time = data.coachSchedu.time
                this.dataForm.siteId = data.coachSchedu.siteId
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
              url: this.$http.adornUrl(`/app/coachschedu/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'coachId': this.dataForm.coachId,
                'time': this.dataForm.time,
                'siteId': this.dataForm.siteId
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
