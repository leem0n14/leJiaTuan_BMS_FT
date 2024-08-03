<template>
  <el-dialog :title="!dataForm.coachId ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <!-- <el-form-item label="用户id" prop="userId">
        <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
      </el-form-item> -->
      <el-form-item label="显示顺序" prop="numbers">
        <el-input v-model="dataForm.numbers" placeholder="显示顺序"></el-input>
      </el-form-item>
      <el-form-item label="教练照片" prop="coachPhoto">
        <!-- <el-input v-model="dataForm.coachPhoto" placeholder="上传成功时获取照片id" :disabled="true">
        </el-input> -->
        <el-upload class="avatar-uploader" ref="upload" :action="url + '/app/uploadFiles/photoUpload'"
          :show-file-list="false" :auto-upload="true" name="file" :on-success="handleAvatarSuccess"
          :before-upload="beforeAvatarUpload" :on-change="chooseImg" :data="{ where: 'coach' }">
          <img v-if="imageUrl" :src="imageUrl" class="avatar">
          <img v-else-if="!imageUrl && dataForm.coachPhoto" :src="cdn + dataForm.coachPhoto" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>

      </el-form-item>

      <el-form-item label="教练姓名" prop="coachName">
        <el-input v-model="dataForm.coachName" placeholder="教练姓名"></el-input>
      </el-form-item>
      <el-form-item label="教练性别" prop="coachSex">
        <!-- <el-input v-model="dataForm.coachSex" placeholder="教练性别"></el-input> -->
        <el-radio v-model="dataForm.coachSex" label="男">男</el-radio>
        <el-radio v-model="dataForm.coachSex" label="女">女</el-radio>
      </el-form-item>
      <el-form-item label="教练电话" prop="coachPhone">
        <el-input v-model="dataForm.coachPhone" placeholder="教练电话"></el-input>
      </el-form-item>
      <el-form-item label="教练性质" prop="coachType">
        <el-radio v-model="dataForm.coachType" label="驾校教练">驾校教练</el-radio>
        <el-radio v-model="dataForm.coachType" label="挂靠教练">挂靠教练</el-radio>
      </el-form-item>
      <el-form-item label="所属驾校" prop="school">
        <el-select v-model="dataForm.school" placeholder="请选择学校" @change="schoolHandle(dataForm.school)">
          <el-option v-for="item in schoolList" :key="item.schoolId" :label="item.schoolName" :value="item.schoolName">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="教学学员数量" prop="stuNum">
        <el-input-number v-model="dataForm.stuNum" label="教学学员数量"></el-input-number>
        <!-- <el-input v-model="dataForm.stuNum" placeholder="教学学员数量"></el-input> -->
      </el-form-item>
      <el-form-item label="评分" prop="evaluation">
        <el-rate v-model="dataForm.evaluation" style="height:100%; display: inline-block;" allow-half></el-rate>
      </el-form-item>
      <el-form-item label="教练所在城市" prop="city">
        <el-input v-model="dataForm.city" placeholder="教练所在城市"></el-input>
      </el-form-item>
      <el-form-item label="教龄" prop="age">
        <el-input v-model="dataForm.age" placeholder="教龄"></el-input>
      </el-form-item>
      <el-form-item label="简介" prop="introduction">
        <el-input type="textarea" v-model="dataForm.introduction" placeholder="简介"></el-input>
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
      url: window.SITE_CONFIG.baseUrl,
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/coach/',
      visible: false,
      coach: {},
      schoolList: [],
      dataForm: {
        numbers: '',
        // coachId: 0,
        userId: '',
        coachPhoto: '',
        coachName: '',
        coachSex: '',
        coachPhone: '',
        coachType: '',
        school: '',
        schoolId: '',
        stuNum: '',
        // statu: '',//小程序是否展示
        // attestation: '',//是否认证
        evaluation: null,
        evaluationNum: '',
        evaluationSum: '',
        city: '',
        age: '',
        introduction: ''
      },
      dataRule: {
        userId: [
          { required: true, message: '用户id不能为空', trigger: 'blur' }
        ],
        // coachPhoto: [
        //   { required: true, message: '教练照片不能为空', trigger: 'blur' }
        // ],
        coachName: [
          { required: true, message: '教练姓名不能为空', trigger: 'blur' }
        ],
        coachSex: [
          { required: true, message: '教练性别不能为空', trigger: 'blur' }
        ],
        coachPhone: [
          { required: true, message: '教练电话不能为空', trigger: 'blur' }
        ],
        coachType: [
          { required: true, message: '教练性质不能为空', trigger: 'blur' }
        ],
        school: [
          { required: true, message: '所属驾校不能为空', trigger: 'blur' }
        ],
        stuNum: [
          { required: true, message: '教学学员数量不能为空', trigger: 'blur' }
        ],
        // evaluation: [
        //   { required: true, message: '评分不能为空', trigger: 'blur' }
        // ],
        // evaluationNum: [
        //   { required: true, message: '评分人数不能为空', trigger: 'blur' }
        // ],
        // evaluationSum: [
        //   { required: true, message: '总评分不能为空', trigger: 'blur' }
        // ],
        city: [
          { required: true, message: '教练所在城市不能为空', trigger: 'blur' }
        ],
        age: [
          { required: true, message: '教龄不能为空', trigger: 'blur' }
        ],
        introduction: [
          { required: true, message: '简介不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  mounted() {
    this.getSchoolList()
  },
  activated() {
    this.getSchoolList()
  },
  methods: {
    getSchoolList() {
      this.$http({
        url: this.$http.adornUrl('/sys/school/getList'),
        method: 'get',
        params: this.$http.adornParams()
      }).then((data) => {
        // console.log(data);
        this.schoolList = data.data.schoolEntities
      })
    },
    schoolHandle(e) {
      // console.log(e);
      for (let i = 0; i < this.schoolList.length; i++) {
        if (this.schoolList[i].schoolName == e) {
          this.dataForm.schoolId = this.schoolList[i].schoolId
        }
      }
    },
    handleAvatarSuccess(res, file) {
      if (res.code == 0) {
        this.dataForm.coachPhoto = res.fileName
        this.imageUrl = URL.createObjectURL(file.raw);
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
      this.dataForm.coachId = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.coachId) {
          this.$http({
            url: this.$http.adornUrl(`/app/coach/info/${this.dataForm.coachId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.numbers = data.coach.numbers
              this.dataForm.userId = data.coach.userId
              this.dataForm.coachPhoto = data.coach.coachPhoto
              this.dataForm.coachName = data.coach.coachName
              this.dataForm.coachSex = data.coach.coachSex
              this.dataForm.coachPhone = data.coach.coachPhone
              this.dataForm.coachType = data.coach.coachType
              this.dataForm.school = data.coach.school
              this.dataForm.schoolId = data.coach.schoolId
              this.dataForm.stuNum = data.coach.stuNum
              this.dataForm.evaluation = data.coach.evaluation
              this.dataForm.evaluationNum = data.coach.evaluationNum
              this.dataForm.evaluationSum = data.coach.evaluationSum
              this.dataForm.city = data.coach.city
              this.dataForm.age = data.coach.age
              this.dataForm.introduction = data.coach.introduction
            }
          })
        }
      })
    },
    chooseImg(file, fileList) {
      console.log(6);
      // this.imageUrl = URL.createObjectURL(file.raw);
    },
    // 表单提交
    dataFormSubmit() {
      let vm = this;
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          // vm.$refs.upload.submit();
          this.$http({
            url: this.$http.adornUrl(`/sys/coach/${!this.dataForm.coachId ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'numbers': parseInt(this.dataForm.numbers),
              'coachId': this.dataForm.coachId || undefined,
              // 'userId': this.dataForm.userId,
              'coachPhoto': this.dataForm.coachPhoto,
              'coachName': this.dataForm.coachName,
              'coachSex': this.dataForm.coachSex,
              'coachPhone': this.dataForm.coachPhone,
              'coachType': this.dataForm.coachType,
              'school': this.dataForm.school,
              'schoolId': this.dataForm.schoolId,
              'stuNum': this.dataForm.stuNum,
              'evaluation': this.dataForm.evaluation,
              // 'evaluationNum': this.dataForm.evaluationNum,
              // 'evaluationSum': this.dataForm.evaluationSum,
              'city': this.dataForm.city,
              'age': this.dataForm.age,
              'introduction': this.dataForm.introduction
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
  },
  // activated() {
  //   console.log('0000');
  // },
  // deactivated() {
  //   console.log('111');
  //   this.imageUrl = ''
  // }
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
