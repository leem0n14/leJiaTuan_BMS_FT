<template>
  <el-dialog :title="!dataForm.schoolId ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="显示顺序" prop="numbers">
        <el-input v-model="dataForm.numbers" placeholder="显示顺序"></el-input>
      </el-form-item>

      <el-form-item label="驾校名称" prop="schoolName">
        <el-input v-model="dataForm.schoolName" placeholder="驾校名称"></el-input>
      </el-form-item>
      <el-form-item label="驾校照片" prop="schoolPhoto">
        <!-- <el-input v-model="dataForm.schoolPhoto" placeholder="上传成功返回随机数图片名" disabled></el-input> -->
        <el-upload :action="url + '/app/uploadFiles/photoUpload'" list-type="picture-card"
          :on-preview="handlePictureCardPreview" :on-remove="handleRemove" :on-success="upLoadSuccess"
          :data="{ where: 'school' }" :file-list="uploadedPhoto">
          <i class="el-icon-plus"></i>
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="dialogImageUrl" alt="">
        </el-dialog>
      </el-form-item>
      <el-form-item label="证书照片" prop="zsPhoto">
        <!-- <el-input v-model="dataForm.zsPhoto" placeholder="证书照片"></el-input> -->
        <el-upload class="avatar-uploader" :action="url + '/app/uploadFiles/photoUpload'" :show-file-list="false"
          :on-success="handleAvatarSuccess" :before-upload="beforeAvatarUpload" :data="{ where: 'zs' }">
          <img v-if="imageUrl" :src="imageUrl" class="avatar">
          <img v-else-if="!imageUrl && dataForm.zsPhoto" :src="cdn + 'zs/' + dataForm.zsPhoto" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="驾校地址" prop="schoolPlace">
        <el-input v-model="dataForm.schoolPlace" placeholder="驾校地址"></el-input>
      </el-form-item>
      <el-form-item label="驾校简介" prop="introduction">
        <el-input v-model="dataForm.introduction" placeholder="驾校简介"></el-input>
      </el-form-item>
      <el-form-item label="配套设施" prop="facilities">
        <el-input v-model="dataForm.facilities" placeholder="配套设施"></el-input>
      </el-form-item>
      <el-form-item label="规模" prop="size">
        <el-input v-model="dataForm.size" placeholder="规模"></el-input>
      </el-form-item>
      <el-form-item label="考场" prop="examPlace">
        <el-input v-model="dataForm.examPlace" placeholder="考场"></el-input>
      </el-form-item>
      <el-form-item label="驾校经度" prop="longitude">
        <el-input v-model="dataForm.longitude" placeholder="驾校经度"></el-input>
      </el-form-item>
      <el-form-item label="驾校纬度" prop="latitude">
        <el-input v-model="dataForm.latitude" placeholder="驾校纬度"></el-input>
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
      imageUrl: '',
      dialogImageUrl: '',
      dialogVisible: false,
      url: window.SITE_CONFIG.baseUrl,
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/',
      visible: false,
      photosList: [],
      dataForm: {
        numbers: '',
        schoolId: 0,
        schoolName: '',
        schoolPhoto: [],
        zsPhoto: '',
        schoolPlace: '',
        introduction: '',
        facilities: '',
        size: '',
        examPlace: '',
        latitude: '',
        longitude: ''
      },
      dataRule: {
        schoolName: [
          { required: true, message: '驾校名称不能为空', trigger: 'blur' }
        ],
        schoolPhoto: [
          { required: true, message: '驾校照片不能为空', trigger: 'blur' }
        ],
        zsPhoto: [
          { required: true, message: '证书照片不能为空', trigger: 'blur' }
        ],
        schoolPlace: [
          { required: true, message: '驾校地址不能为空', trigger: 'blur' }
        ],
        introduction: [
          { required: true, message: '驾校简介不能为空', trigger: 'blur' }
        ],
        facilities: [
          { required: true, message: '配套设施不能为空', trigger: 'blur' }
        ],
        size: [
          { required: true, message: '规模不能为空', trigger: 'blur' }
        ],
        examPlace: [
          { required: true, message: '考场不能为空', trigger: 'blur' }
        ],
        latitude: [
          { required: true, message: '驾校纬度不能为空', trigger: 'blur' }
        ],
        longitude: [
          { required: true, message: '驾校经度不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  computed: {
    uploadedPhoto() {
      let arr = []
      for (let i = 0; i < this.dataForm.schoolPhoto.length; i++) {
        arr.push({ name: this.dataForm.schoolPhoto[i], url: this.cdn + 'school/' + this.dataForm.schoolPhoto[i] })
      }
      return arr
    }
  },
  methods: {
    handleAvatarSuccess(res, file) {
      if (res.code == 0) {
        this.dataForm.zsPhoto = res.fileName
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
    handleRemove(file, fileList) {
      console.log(file.name, fileList);
      this.dataForm.schoolPhoto = this.dataForm.schoolPhoto.filter(k => k != file.name)


    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    upLoadSuccess(res, file) {
      if (res.code == 0) {
        this.dataForm.schoolPhoto.push(res.fileName);
        // this.imageUrl = URL.createObjectURL(file.raw);
        // this.imageUrl = null
        this.$message({
          message: '上传成功',
          type: 'success'
        })

      }
      else {
        this.$message.error('上传失败' + ',错误信息:' + res.msg)
      }
    },
    init(id) {
      this.dataForm.schoolId = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.schoolId) {
          this.$http({
            url: this.$http.adornUrl(`/sys/school/info/${this.dataForm.schoolId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.numbers = data.school.numbers
              this.dataForm.schoolName = data.school.schoolName
              this.dataForm.schoolPhoto = data.school.schoolPhoto
              this.dataForm.zsPhoto = data.school.zsPhoto
              this.dataForm.schoolPlace = data.school.schoolPlace
              this.dataForm.introduction = data.school.introduction
              this.dataForm.facilities = data.school.facilities
              this.dataForm.size = data.school.size
              this.dataForm.examPlace = data.school.examPlace
              this.dataForm.latitude = data.school.latitude
              this.dataForm.longitude = data.school.longitude


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
            url: this.$http.adornUrl(`/sys/school/${!this.dataForm.schoolId ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'numbers': parseInt(this.dataForm.numbers),
              'schoolId': this.dataForm.schoolId || undefined,
              'schoolName': this.dataForm.schoolName,
              'schoolPhoto': this.dataForm.schoolPhoto,
              'zsPhoto': this.dataForm.zsPhoto,
              'schoolPlace': this.dataForm.schoolPlace,
              'introduction': this.dataForm.introduction,
              'facilities': this.dataForm.facilities,
              'size': this.dataForm.size,
              'examPlace': this.dataForm.examPlace,
              'latitude': this.dataForm.latitude,
              'longitude': this.dataForm.longitude
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