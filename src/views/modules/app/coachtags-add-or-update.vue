<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="教练ID" prop="coachId">
      <el-input v-model="dataForm.coachId" placeholder="教练ID"></el-input>
    </el-form-item>
    <el-form-item label="评价标签" prop="tag">
      <el-input v-model="dataForm.tag" placeholder="评价标签"></el-input>
    </el-form-item>
    <el-form-item label="标签个数" prop="tagNum">
      <el-input v-model="dataForm.tagNum" placeholder="标签个数"></el-input>
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
          tag: '',
          tagNum: ''
        },
        dataRule: {
          coachId: [
            { required: true, message: '教练ID不能为空', trigger: 'blur' }
          ],
          tag: [
            { required: true, message: '评价标签不能为空', trigger: 'blur' }
          ],
          tagNum: [
            { required: true, message: '标签个数不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/coachtags/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.coachId = data.coachTags.coachId
                this.dataForm.tag = data.coachTags.tag
                this.dataForm.tagNum = data.coachTags.tagNum
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
              url: this.$http.adornUrl(`/app/coachtags/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'coachId': this.dataForm.coachId,
                'tag': this.dataForm.tag,
                'tagNum': this.dataForm.tagNum
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
