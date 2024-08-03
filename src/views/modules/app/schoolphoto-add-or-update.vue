<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="驾校id" prop="schoolId">
      <el-input v-model="dataForm.schoolId" placeholder="驾校id"></el-input>
    </el-form-item>
    <el-form-item label="驾校图片" prop="photo">
      <el-input v-model="dataForm.photo" placeholder="驾校图片"></el-input>
    </el-form-item>
    <el-form-item label="图片序号" prop="no">
      <el-input v-model="dataForm.no" placeholder="图片序号"></el-input>
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
          schoolId: '',
          photo: '',
          no: ''
        },
        dataRule: {
          schoolId: [
            { required: true, message: '驾校id不能为空', trigger: 'blur' }
          ],
          photo: [
            { required: true, message: '驾校图片不能为空', trigger: 'blur' }
          ],
          no: [
            { required: true, message: '图片序号不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/schoolphoto/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.schoolId = data.schoolPhoto.schoolId
                this.dataForm.photo = data.schoolPhoto.photo
                this.dataForm.no = data.schoolPhoto.no
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
              url: this.$http.adornUrl(`/app/schoolphoto/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'schoolId': this.dataForm.schoolId,
                'photo': this.dataForm.photo,
                'no': this.dataForm.no
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
