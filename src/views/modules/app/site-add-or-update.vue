<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="场地名称" prop="siteName">
        <el-input v-model="dataForm.siteName" placeholder="场地名称"></el-input>
      </el-form-item>
      <el-form-item label="场地首页照片" prop="sitePhoto">

        <el-upload ref="upLoad" class="avatar-uploader" :action="url + '/app/uploadFiles/photoUpload'"
          :data="{ where: 'site' }" :on-success="handleAvatarSuccess" :show-file-list="false">
          <img v-if="imageUrl" :src="imageUrl" class="avatar">
          <img v-else-if="!imageUrl && dataForm.sitePhoto" :src="cdn + dataForm.sitePhoto" class="avatar" alt="">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="场地地址" prop="place">
        <el-input v-model="dataForm.place" placeholder="场地地址"></el-input>
      </el-form-item>
      <el-form-item label="场地所属驾校" prop="schoolName">
        <!-- <el-tag v-for="k in dataForm.schoolList" :key="k.schoolId" style="margin: 0 5px 0;">{{ k.schoolName }}</el-tag> -->
        <el-button type="primary" @click="dialogVisible = true">选择驾校 <i class="el-icon-plus"></i></el-button>
      </el-form-item>
      <el-form-item label="经度" prop="longitude">
        <el-input v-model="dataForm.longitude" placeholder="经度"></el-input>
      </el-form-item>
      <el-form-item label="纬度" prop="latitude">
        <el-input v-model="dataForm.latitude" placeholder="纬度"></el-input>
      </el-form-item>
      <el-form-item label="签到开始时间" :prop="dataForm.signsTime == '' && signsTime == '' ? 'signsTime' : ''">
        <!-- <el-input v-model="dataForm.signsTime" placeholder="签到开始时间"></el-input> -->
        <span v-if="dataForm.signsTime == ''">{{ signsTime }}</span>
        <el-time-picker v-model="dataForm.signsTime" :placeholder="(dataForm.signsTime ? '修改' : '') + '签到开始时间'"
          value-format="timestamp">
        </el-time-picker>
      </el-form-item>
      <el-form-item label="签到结束时间" :prop="dataForm.signeTime && signeTime == '' ? 'signeTime' : ''">
        <!-- <el-input v-model="dataForm.signeTime" placeholder="签到结束时间"></el-input> -->
        <span v-if="dataForm.signeTime == ''">{{ signeTime }}</span>
        <el-time-picker v-model="dataForm.signeTime" :placeholder="(dataForm.signeTime ? '修改' : '') + '签到结束时间'"
          value-format="timestamp" start-placeholder="dataForm.signsTime">
        </el-time-picker>
      </el-form-item>
      <el-form-item label="签退开始时间" :prop="dataForm.leftsTime && leftsTime == '' ? 'leftsTime' : ''">
        <!-- <el-input v-model="dataForm.leftsTime" placeholder="签退开始时间"></el-input> -->
        <span v-if="dataForm.leftsTime == ''">{{ leftsTime }}</span>
        <el-time-picker v-model="dataForm.leftsTime" :placeholder="(dataForm.leftsTime ? '修改' : '') + '签退开始时间'"
          value-format="timestamp">
        </el-time-picker>
      </el-form-item>
      <el-form-item label="签退结束时间" :prop="dataForm.lefteTime && lefteTime == '' ? 'lefteTime' : ''">
        <!-- <el-input v-model="dataForm.lefteTime" placeholder="签退结束时间"></el-input> -->
        <span v-if="dataForm.lefteTime == ''">{{ lefteTime }}</span>
        <el-time-picker v-model="dataForm.lefteTime" :placeholder="(dataForm.lefteTime ? '修改' : '') + '签退结束时间'"
          :value-format="'timestamp'" start-placeholder="dataForm.leftsTime">
        </el-time-picker>
      </el-form-item>
      <el-form-item label="场地简介" prop="introduction">
        <el-input v-model="dataForm.introduction" placeholder="场地简介"></el-input>
      </el-form-item>
    </el-form>

    <el-dialog :visible.sync="dialogVisible" width="60%" append-to-body>
      <span>选择驾校</span>
      <div>
        <el-checkbox-group v-model="dataForm.schoolIdList" @change="schoolsCheck(dataForm.schoolIdList)">
          <el-checkbox v-for="k in schools" :label="k.schoolId" :key="k.schoolId">{{ k.schoolName }}</el-checkbox>
        </el-checkbox-group>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>

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
      dialogVisible: false,
      visible: false,
      imageUrl: null,
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/site/',
      dataForm: {
        id: 0,
        siteName: '',
        sitePhoto: '',
        place: '',
        schoolIdList: [],
        longitude: '',
        latitude: '',
        signsTime: '',
        signeTime: '',
        leftsTime: '',
        lefteTime: '',
        introduction: '',
      },

      signsTime: '',
      signeTime: '',
      leftsTime: '',
      lefteTime: '',
      schoolNameList: [],
      schools: [],
      url: window.SITE_CONFIG.baseUrl,
      dataRule: {
        siteName: [
          { required: true, message: '场地名称不能为空', trigger: 'blur' }
        ],
        sitePhoto: [
          { required: true, message: '场地首页照片不能为空', trigger: 'blur' }
        ],
        place: [
          { required: true, message: '场地地址不能为空', trigger: 'blur' }
        ],
        schoolIdList: [
          { required: true, message: '场地所属驾校不能为空', trigger: 'blur' }
        ],
        longitude: [
          { required: true, message: '经度不能为空', trigger: 'blur' }
        ],
        latitude: [
          { required: true, message: '纬度不能为空', trigger: 'blur' }
        ],
        signsTime: [
          { required: true, message: '签到开始时间不能为空', trigger: 'blur' }
        ],
        signeTime: [
          { required: true, message: '签到结束时间不能为空', trigger: 'blur' }
        ],
        leftsTime: [
          { required: true, message: '签退开始时间不能为空', trigger: 'blur' }
        ],
        lefteTime: [
          { required: true, message: '签退结束时间不能为空', trigger: 'blur' }
        ],
        introduction: [
          { required: true, message: '场地简介不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  mounted() {
    this.getSchoolList()
  },

  methods: {
    handleAvatarSuccess(res, file) {
      this.imageUrl = URL.createObjectURL(file.raw);
      if (res.code == 0) {
        this.$message.success('上传成功')
        this.dataForm.sitePhoto = res.fileName
      } else {
        this.$message.error(res.msg)
      }
    },
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/site/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams(),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.siteName = data.site.siteName
              this.dataForm.sitePhoto = data.site.sitePhoto
              this.dataForm.place = data.site.place
              this.dataForm.schoolId = data.site.schoolId
              this.dataForm.schoolList = data.site.schoolList
              this.dataForm.longitude = data.site.longitude
              this.dataForm.latitude = data.site.latitude
              this.dataForm.signsTime = data.site.signsTimes
              this.dataForm.signeTime = data.site.signeTimes
              this.dataForm.leftsTime = data.site.leftsTimes
              this.dataForm.lefteTime = data.site.lefteTimes
              this.dataForm.introduction = data.site.introduction

              let arr = []
              for (let i = 0; i < this.dataForm.schoolList.length; i++) {
                arr.push(this.dataForm.schoolList[i].schoolId)
              }
              this.dataForm.schoolIdList = arr
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
            url: this.$http.adornUrl(`/sys/site/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'siteName': this.dataForm.siteName,
              'sitePhoto': this.dataForm.sitePhoto,
              'place': this.dataForm.place,
              // 'schoolId': this.dataForm.schoolId,
              // 'schoolName': this.dataForm.schoolName,
              'longitude': this.dataForm.longitude,
              'latitude': this.dataForm.latitude,
              'signsTimes': this.dataForm.signsTime != '' ? this.dataForm.signsTime : this.signsTime,
              'signeTimes': this.dataForm.signeTime != '' ? this.dataForm.signeTime : this.signeTime,
              'leftsTimes': this.dataForm.leftsTime != '' ? this.dataForm.leftsTime : this.leftsTime,
              'lefteTimes': this.dataForm.lefteTime != '' ? this.dataForm.lefteTime : this.lefteTime,
              'introduction': this.dataForm.introduction,
              'schoolIdList': this.dataForm.schoolIdList,
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
    schoolsCheck(e) {
      // console.log(e);
      this.dataForm.schoolName = e
      // let arr = []
      // let narr = []
      // for (let i = 0; i < e.length; i++) {
      //   arr.push(e[i].schoolId)
      //   narr.push(e[i].schoolName)
      // }
      // this.schoolNameList = narr
      // this.dataForm.schoolIdList = arr
      // this.dataForm.schoolId = arr
    },
    //拿驾校列表
    // 
    getSchoolList() {
      this.$http({
        url: this.$http.adornUrl('/sys/school/getList'),
        method: 'get',
        params: this.$http.adornParams({
          'page': 1,
          'limit': 20,
        })
      }).then((res) => {
        // console.log(res);
        this.schools = res.data.schoolEntities
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