<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible" width="80%">
    <el-form label-position="top" :model="dataForm" :rules="dataRule" ref="dataForm"
      @keyup.enter.native="dataFormSubmit()" label-width="120px">
      <el-form-item label="视频名字" prop="name" label-width="120">
        <el-input v-model="dataForm.name" placeholder="视频名字" style="width: 600px;"></el-input>
      </el-form-item>
      <el-form-item label="显示顺序" prop="numbers" label-width="120">
        <el-input v-model="dataForm.numbers" placeholder="显示顺序" style="width: 200px;"></el-input>
      </el-form-item>
      <el-form-item label="视频科目" prop="class1" label-width="120">
        <!-- <el-input v-model="dataForm.class" placeholder="视频科目"></el-input> -->
        <el-radio-group v-model="dataForm.class1">
          <el-radio :label="'科目二'">科目二</el-radio>
          <el-radio :label="'科目三'">科目三</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="视频描述" prop="content" label-width="120">
        <el-input v-model="dataForm.content" placeholder="视频描述" style="width: 600px;"></el-input>
      </el-form-item>
      <el-form-item label="视频富文本" prop="richText" label-width="120">
        <editor-bar :catchData="catchData" :content="editorContent" :describe="dataForm.richText"></editor-bar>
      </el-form-item>
      <el-form-item label="是否开启" prop="statu" label-width="120">
        <el-radio-group v-model="dataForm.statu">
          <el-radio :label="0">关闭</el-radio>
          <el-radio :label="1">开启</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="视频封面(250×140)" prop="photo" label-width="120">
        <el-upload class="avatar-uploader" :action="url + '/app/uploadFiles/photoUpload'" :show-file-list="false"
          :on-success="handleAvatarSuccess" :data="{ where: 'subject23' }" :limit="1" ref="uploadPhoto">
          <img v-if="dataForm.photo" :src="url + '/super/subject23/' + dataForm.photo" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="上传视频" prop="video" label-width="120">
        <el-upload class="upload-demo" ref="upload" :action="url + '/app/uploadFiles/photoUpload'"
          :on-success="upLoadSuccess" :on-remove="handleRemove" :file-list="fileList" :auto-upload="false"
          :data="{ where: 'subject23' }" :limit="1">
          <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
          <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">上传到服务器</el-button>
          <div slot="tip" class="el-upload__tip">上传视频文件</div>
        </el-upload>
      </el-form-item>
    </el-form>

    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()" :disabled="btnBool">确定</el-button>
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
        name: '',
        video: '',
        class1: '',
        statu: '',
        content: '',
        numbers: '',
        photo: '',
        richText: '',
      },
      fileList: [],
      url: window.SITE_CONFIG.baseUrl,
      dataRule: {
        name: [
          { required: true, message: '视频名字不能为空', trigger: 'blur' }
        ],
        numbers: [
          { required: true, message: '显示顺序不能为空', trigger: 'blur' }
        ],
        video: [
          { required: true, message: '视频文件不能为空', trigger: 'blur' }
        ],
        photo: [
          { required: true, message: '视频封面不能为空', trigger: 'blur' }
        ],
        class1: [
          { required: true, message: '视频科目不能为空', trigger: 'blur' }
        ],

        statu: [
          { required: true, message: '请选择是否开启', trigger: 'blur' }
        ],
        content: [
          { required: true, message: '请输入视频描述', trigger: 'blur' }
        ],
        richText: [
          { required: true, message: '请输入视频富文本', trigger: 'blur' }
        ],
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
      this.dataForm.richText = e
    },
    //富文本中的内容
    editorContent(e) {


    },
    submitUpload() {
      this.$refs.upload.submit();
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handleAvatarSuccess(res, file) {
      // console.log(res);
      this.dataForm.photo = res.fileName;
      this.$refs.uploadPhoto.clearFiles()
    },
    upLoadSuccess(res) {
      console.log(res);
      if (res.code == 0) {
        this.$message({
          message: '上传成功',
          type: 'success',
        })
        this.dataForm.video = res.fileName
      } else {
        this.$message.error(res.msg)
      }
      this.$refs.upload.clearFiles()

    },

    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/video/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code == 0) {
              this.dataForm.name = data.video.name
              this.dataForm.video = data.video.video
              this.dataForm.class1 = data.video.class1
              this.dataForm.statu = data.video.statu
              this.dataForm.content = data.video.content
              this.dataForm.numbers = data.video.numbers
              this.dataForm.photo = data.video.photo
              this.dataForm.richText = data.video.richText
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
            url: this.$http.adornUrl(`/sys/video/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'name': this.dataForm.name,
              'video': this.dataForm.video,
              'class1': this.dataForm.class1,
              'statu': this.dataForm.statu,
              'content': this.dataForm.content,
              'numbers': parseInt(this.dataForm.numbers),
              'photo': this.dataForm.photo,
              'richText': this.dataForm.richText,
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
              this.dataForm.video = ''
            } else {
              this.$message.error(data.msg)
            }
          })
        }
      })
    }
  },
  computed: {
    btnBool() {
      return this.dataForm.class1 == '' || this.dataForm.video == '' || this.dataForm.name == ''

    }
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
  // width: 178px;
  height: 178px;
  display: block;
}
</style>