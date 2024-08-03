<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="轮播图片(750×280)" prop="photo">
        <!-- <el-input v-model="dataForm.photo" placeholder="上传成功返回随机数图片名" disabled></el-input> -->
        <el-upload class="avatar-uploader" :action="url + '/app/uploadFiles/photoUpload'" :show-file-list="false"
          :on-success="handleAvatarSuccess" :before-upload="beforeAvatarUpload" :data="{ where: 'rotat' }">
          <img v-if="imageUrl" :src="imageUrl" class="avatar">
          <img v-else-if="!imageUrl && dataForm.photo" :src="cdn + dataForm.photo" alt="" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="是否启用" prop="statu">
        <el-radio v-model="dataForm.statu" :label="0">不启用</el-radio>
        <el-radio v-model="dataForm.statu" :label="1">启用</el-radio>
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
      url: window.SITE_CONFIG.baseUrl,
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/rotat/',
      imageUrl: '',
      visible: false,
      dataForm: {
        id: 0,
        photo: '',
        statu: ''
      },
      dataRule: {
        photo: [
          { required: true, message: '轮播图片不能为空', trigger: 'blur' }
        ],
        statu: [
          { required: true, message: '图片是否启用{0 不启用 1启用}不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    handleAvatarSuccess(res, file) {

      if (res.code == 0) {
        this.dataForm.photo = res.fileName
        this.imageUrl = URL.createObjectURL(file.raw);
        this.$message({
          message: '上传成功',
          type: 'success'
        })

      }
      else {
        this.$message.error('上传失败' + ',错误信息:' + res.msg)
      }
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === 'image/jpeg';
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG) {
        this.$message.error('上传头像图片只能是 JPG 格式!');
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!');
      }
      return isJPG && isLt2M;
    },
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/rotat/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.photo = data.rotat.photo
              this.dataForm.statu = data.rotat.statu
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
            url: this.$http.adornUrl(`/sys/rotat/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'photo': this.dataForm.photo,
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


<style>
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
  width: 178px;
  height: 178px;
  display: block;
}
</style>