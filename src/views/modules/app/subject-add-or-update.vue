<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="问题" prop="titleInformation">
      <el-input v-model="dataForm.titleInformation" placeholder="问题"></el-input>
    </el-form-item>
    <el-form-item label="图片" prop="picture">
      <el-input v-model="dataForm.picture" placeholder="图片"></el-input>
    </el-form-item>
    <el-form-item label="第二张图" prop="scpicture">
      <el-input v-model="dataForm.scpicture" placeholder="第二张图"></el-input>
    </el-form-item>
    <el-form-item label="单选还是多选{0 单选 1多选}" prop="form">
      <el-input v-model="dataForm.form" placeholder="单选还是多选{0 单选 1多选}"></el-input>
    </el-form-item>
    <el-form-item label="" prop="a">
      <el-input v-model="dataForm.a" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="b">
      <el-input v-model="dataForm.b" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="c">
      <el-input v-model="dataForm.c" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="d">
      <el-input v-model="dataForm.d" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="答案" prop="answer">
      <el-input v-model="dataForm.answer" placeholder="答案"></el-input>
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
          titleInformation: '',
          picture: '',
          scpicture: '',
          form: '',
          a: '',
          b: '',
          c: '',
          d: '',
          answer: ''
        },
        dataRule: {
          titleInformation: [
            { required: true, message: '问题不能为空', trigger: 'blur' }
          ],
          picture: [
            { required: true, message: '图片不能为空', trigger: 'blur' }
          ],
          scpicture: [
            { required: true, message: '第二张图不能为空', trigger: 'blur' }
          ],
          form: [
            { required: true, message: '单选还是多选{0 单选 1多选}不能为空', trigger: 'blur' }
          ],
          a: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          b: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          c: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          d: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          answer: [
            { required: true, message: '答案不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/sys/subject/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.titleInformation = data.subject.titleInformation
                this.dataForm.picture = data.subject.picture
                this.dataForm.scpicture = data.subject.scpicture
                this.dataForm.form = data.subject.form
                this.dataForm.a = data.subject.a
                this.dataForm.b = data.subject.b
                this.dataForm.c = data.subject.c
                this.dataForm.d = data.subject.d
                this.dataForm.answer = data.subject.answer
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
              url: this.$http.adornUrl(`/sys/subject/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'titleInformation': this.dataForm.titleInformation,
                'picture': this.dataForm.picture,
                'scpicture': this.dataForm.scpicture,
                'form': this.dataForm.form,
                'a': this.dataForm.a,
                'b': this.dataForm.b,
                'c': this.dataForm.c,
                'd': this.dataForm.d,
                'answer': this.dataForm.answer
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
