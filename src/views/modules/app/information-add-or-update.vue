<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="标题" prop="title">
        <el-input v-model="dataForm.title" placeholder="标题"></el-input>
      </el-form-item>
      <el-form-item label="封面" prop="photo">
        <span>（考试秘籍：138*96，学车技巧：380*110）</span>
        <el-upload :show-file-list="false" :action="cdn + '/app/uploadFiles/photoUpload'" :on-success="handlePhotoSuccess"
          :before-upload="beforePhotoUpload" class="avatar-uploader" :data="{
            where: 'information'
          }">
          <img v-if="dataForm.photo" :src="`${cdn}/super/information/${dataForm.photo}`" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
        <!-- <el-input v-model="dataForm.photo" placeholder="封面"></el-input> -->
      </el-form-item>
      <el-form-item label="富文本" prop="text">
        <!-- <el-input v-model="dataForm.text" placeholder="富文本"></el-input> -->
        <editor-bar :catchData="catchData" :content="editorContent" :describe="dataForm.text"></editor-bar>
      </el-form-item>
      <el-form-item label="类型栏目" prop="type">
        <el-radio-group v-model="dataForm.type" @input="changeType(dataForm.type)">
          <el-radio :label="1">考试秘籍</el-radio>
          <el-radio :label="2">学车技巧</el-radio>
          <el-radio :label="3">答题技巧</el-radio>
        </el-radio-group>
        <!-- <el-input v-model="dataForm.type" placeholder="类型栏目{ 1考试秘籍 2学车技巧 }"></el-input> -->
      </el-form-item>
      <!-- <el-form-item label="分组标题" :prop="dataForm.type === 3 ? 'groupText' : ''" v-if="dataForm.type === 3">
        <el-input v-model="dataForm.groupText"></el-input>
      </el-form-item> -->
      <el-form-item label="科目" prop="subject" v-if="dataForm.type && dataForm.type === 3">
        <el-radio-group v-model="dataForm.subject">
          <el-radio :label="1">科目一</el-radio>
          <el-radio :label="4">科目四</el-radio>
        </el-radio-group>
        <!-- <el-input v-model="dataForm.subject" placeholder="科目"></el-input> -->
      </el-form-item>
      <el-form-item label="科目" prop="subject" v-else-if="dataForm.type && (dataForm.type === 1 || dataForm.type === 2)">
        <el-radio-group v-model="dataForm.subject">
          <el-radio :label="2">科目二</el-radio>
          <el-radio :label="3">科目三</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="是否显示" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">不显示</el-radio>
          <el-radio :label="1">显示</el-radio>
        </el-radio-group>
        <!-- <el-input v-model="dataForm.status" placeholder="是否显示{0 不显示 1显示}"></el-input> -->
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
      cdn: window.SITE_CONFIG.baseUrl,
      visible: false,
      dataForm: {
        id: 0,
        title: '',
        photo: '',
        text: '',
        subject: '',
        type: '',
        status: '',
        // groupText: '',
      },
      dataRule: {
        title: [
          { required: true, message: '标题不能为空', trigger: 'blur' }
        ],
        photo: [
          { required: true, message: '封面不能为空', trigger: 'blur' }
        ],
        text: [
          { required: true, message: '富文本不能为空', trigger: 'blur' }
        ],
        subject: [
          { required: true, message: '科目不能为空', trigger: 'blur' }
        ],
        type: [
          { required: true, message: '类型栏目不能为空', trigger: 'blur' }
        ],
        status: [
          { required: true, message: '是否显示不能为空', trigger: 'blur' }
        ],
        // groupText: [
        //   { required: true, message: '答题技巧分组标题不能为空', trigger: 'blur' }
        // ]
      }
    }
  },
  components: {
    EditorBar
  },
  methods: {
    changeType(e) {
      console.log(e);
      this.dataForm.subject = ''
    },
    handlePhotoSuccess(res) {
      console.log(res);
      this.dataForm.photo = res.fileName
      // this.imageUrl = URL.createObjectURL(file.raw);
    },
    beforePhotoUpload(file) {
      // const isJPG = file.type === 'image/jpeg';
      const isLt2M = file.size / 1024 / 1024 < 2;
      if (!isLt2M) {
        this.$message.error('上传封面图片大小不能超过 2MB!');
      }
      return isLt2M;
    },
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/information/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.title = data.information.title
              this.dataForm.photo = data.information.photo
              this.dataForm.text = data.information.text
              this.dataForm.subject = data.information.subject
              this.dataForm.type = data.information.type
              this.dataForm.status = data.information.status
              // this.dataForm.groupText = data.information.groupText
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
            url: this.$http.adornUrl(`/sys/information/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'title': this.dataForm.title,
              'photo': this.dataForm.photo,
              'text': this.dataForm.text,
              'subject': this.dataForm.subject,
              'type': this.dataForm.type,
              'status': this.dataForm.status,
              // groupText: this.dataForm.groupText
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
    },
    // 监听富文本的输入
    catchData(e) {
      // console.log("111", e);
      this.dataForm.text = e
    },
    //富文本中的内容
    editorContent(e) {
      console.log("222", e);

    },
  }
}
</script>


<style lang="scss">
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}

.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}

.avatar {
  min-width: 178px;
  height: 178px;
  display: block;
}
</style>