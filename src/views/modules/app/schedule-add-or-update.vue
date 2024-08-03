<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="stuId">
      <el-input v-model="dataForm.stuId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="星期名" prop="week">
      <el-input v-model="dataForm.week" placeholder="星期名"></el-input>
    </el-form-item>
    <el-form-item label="时间段" prop="time">
      <el-input v-model="dataForm.time" placeholder="时间段"></el-input>
    </el-form-item>
    <el-form-item label="是否有空" prop="free">
      <el-input v-model="dataForm.free" placeholder="是否有空"></el-input>
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
          week: '',
          time: '',
          free: ''
        },
        dataRule: {
          stuId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          week: [
            { required: true, message: '星期名不能为空', trigger: 'blur' }
          ],
          time: [
            { required: true, message: '时间段不能为空', trigger: 'blur' }
          ],
          free: [
            { required: true, message: '是否有空不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/schedule/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.stuId = data.schedule.stuId
                this.dataForm.week = data.schedule.week
                this.dataForm.time = data.schedule.time
                this.dataForm.free = data.schedule.free
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
              url: this.$http.adornUrl(`/app/schedule/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'stuId': this.dataForm.stuId,
                'week': this.dataForm.week,
                'time': this.dataForm.time,
                'free': this.dataForm.free
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
