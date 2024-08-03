<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="一级目录" prop="parentId">
        <!-- <el-input v-model="dataForm.parentId" placeholder="父id"></el-input> -->
        <el-select v-model="dataForm.parentId" clearable placeholder="请选择一级目录">
          <el-option v-for="item in signParentList1" :key="item.id" :label="item.text" :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="照片" prop="photo">
        <!-- <el-input v-model="dataForm.photo" placeholder="照片"></el-input> -->
        <el-upload :show-file-list="false" :action="cdn + '/app/uploadFiles/photoUpload'" :on-success="handlePhotoSuccess"
          :before-upload="beforePhotoUpload" class="avatar-uploader" :data="{
            where: 'sign'
          }">
          <img v-if="dataForm.photo" :src="`${cdn}/super/sign/${dataForm.photo}`" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="文字" prop="text">
        <el-input v-model="dataForm.text" placeholder="文字"></el-input>
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
      cdn: window.SITE_CONFIG.baseUrl,
      visible: false,
      dataForm: {
        id: 0,
        parentId: '',
        photo: '',
        text: ''
      },
      dataRule: {
        parentId: [
          { required: true, message: '一级目录不能为空', trigger: 'blur' }
        ],
        photo: [
          { required: true, message: '照片不能为空', trigger: 'blur' }
        ],
        text: [
          { required: true, message: '文字不能为空', trigger: 'blur' }
        ]
      },
      signParentList1: [],
      signParentList2: [],
    }
  },
  mounted() {
    console.log('mounted');
    this.getSignParent1()
    this.getSignParent2()
  },
  methods: {
    handlePhotoSuccess(res) {
      // console.log(res);
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
    //拿一级目录
    getSignParent1() {
      // console.log('?');
      this.$http({
        url: this.$http.adornUrl('/sys/sign2/getSign1'),
        method: 'get',
        params: this.$http.adornParams({
        })
      }).then((res) => {
        const { data } = res
        this.signParentList1 = data.sign1EntityList
      }).catch((err) => {
        this.$message.error('获取一级目录失败')
      })
    },
    //拿二级级目录
    getSignParent2(id) {
      // console.log('?');
      this.$http({
        url: this.$http.adornUrl('/sys/sign/getSign2'),
        method: 'get',
        params: this.$http.adornParams({
          id,
        })
      }).then((res) => {
        const { data } = res
        this.signParentList2 = data.sign2EntityList
      }).catch((err) => {
        this.$message.error('获取二级目录失败')
      })
    },
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/sign2/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.parentId = data.sign2.parentId
              this.dataForm.photo = data.sign2.photo
              this.dataForm.text = data.sign2.text
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
            url: this.$http.adornUrl(`/sys/sign2/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'parentId': this.dataForm.parentId,
              'photo': this.dataForm.photo,
              'text': this.dataForm.text
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