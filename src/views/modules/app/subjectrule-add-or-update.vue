<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="80px">
      <el-form-item label="考规" prop="rule">
        <!-- <el-input v-model="dataForm.rule" placeholder="考规"></el-input> -->
        <editor-bar :catchData="catchData" :content="editorContent" :describe="dataForm.rule"></editor-bar>
      </el-form-item>
      <el-form-item label="科目" prop="subject">
        <el-input v-model="dataForm.subject" placeholder="科目"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import EditorBar from "@/components/editoritem/editoritem"
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        id: 0,
        rule: '',
        subject: '',
      },
      dataRule: {
        rule: [
          { required: true, message: '考规不能为空', trigger: 'blur' }
        ],
        subject: [
          { required: true, message: '科目不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  components: {
    EditorBar
  },
  methods: {
    // 监听富文本的输入
    catchData(e) {
      // console.log("111", e);
      this.dataForm.rule = e
    },
    //富文本中的内容
    editorContent(e) {
      // console.log("222", e)
    },
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/subjectrule/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.rule = data.subjectRule.rule
              this.dataForm.subject = data.subjectRule.subject
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
            url: this.$http.adornUrl(`/sys/subjectrule/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'rule': this.dataForm.rule,
              'subject': this.dataForm.subject
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
