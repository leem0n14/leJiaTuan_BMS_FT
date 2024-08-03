<template>
  <el-dialog :title="!dataForm.stuId ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
      label-width="120px">
      <!-- <el-form-item label="用户ID" prop="userId">
        <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
      </el-form-item> -->
      <el-form-item label="学员姓名" prop="stuName">
        <el-input v-model="dataForm.stuName" placeholder="学员姓名"></el-input>
      </el-form-item>
      <el-form-item label="学员性别" prop="stuSex">
        <!-- <el-input v-model="dataForm.stuSex" placeholder="学员性别"></el-input> -->
        <el-radio v-model="dataForm.stuSex" label="男">男</el-radio>
        <el-radio v-model="dataForm.stuSex" label="女">女</el-radio>
      </el-form-item>
      <el-form-item label="学员电话" prop="stuPhone">
        <el-input v-model="dataForm.stuPhone" placeholder="学员电话" maxlength="11"></el-input>
      </el-form-item>
      <el-form-item label="报班车型" prop="classType">
        <!-- <el-input v-model="dataForm.classType" placeholder="报班车型"></el-input> -->
        <el-select v-model="dataForm.classType" placeholder="请选择车型">
          <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="所属驾校" prop="schoolName">
        <el-select v-model="dataForm.schoolName" placeholder="请选择学校" @change="schoolHandle(dataForm.schoolName)">
          <el-option v-for="item in schoolList" :key="item.schoolId" :label="item.schoolName" :value="item.schoolName">
          </el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="教练姓名" prop="coachName">
        <el-select v-model="dataForm.coachName" placeholder="请选择教练" @focus="coachFocus()"
          @change="coachHandle(dataForm.coachName)">
          <el-option v-for="item in coachList" :key="item.coachId" :label="item.coachName" :value="item.coachName">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="学员等级" prop="stuLevel">
        <!-- <el-input v-model="dataForm.stuLevel" placeholder="学员等级{0 普通 1VIP }"></el-input> -->
        <el-radio v-model="dataForm.stuLevel" :label="0">普通</el-radio>
        <el-radio v-model="dataForm.stuLevel" :label="1">VIP</el-radio>
      </el-form-item>
      <el-form-item label="身份证号" prop="cardId">
        <el-input v-model="dataForm.cardId" placeholder="身份证号"></el-input>
      </el-form-item>
      <el-form-item label="学员身份证地址" prop="cardAd">
        <el-input v-model="dataForm.cardAd" placeholder="学员身份证地址"></el-input>
      </el-form-item>
      <el-form-item label="身份证正面照" prop="cardFront">
        <!-- <el-input v-model="dataForm.cardFront" placeholder="上传成功返回随机数图片名" :disabled="true"></el-input> -->

        <el-upload class="avatar-uploader" :action="url + '/app/uploadFiles/photoUpload'" :show-file-list="false"
          :on-success="handleAvatarSuccess_1" :before-upload="beforeAvatarUpload" :data="{ where: 'student' }">
          <img v-if="imageUrl_1" :src="imageUrl_1" class="avatar">
          <img v-else-if="!imageUrl_1 && dataForm.cardFront" :src="cdn + dataForm.cardFront" alt="" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>

      </el-form-item>
      <el-form-item label="身份证背面照" prop="cardBack">
        <!-- <el-input v-model="dataForm.cardBack" placeholder="上传成功返回随机数图片名" :disabled="true"></el-input> -->

        <el-upload class="avatar-uploader" :action="url + '/app/uploadFiles/photoUpload'" :show-file-list="false"
          :on-success="handleAvatarSuccess_2" :before-upload="beforeAvatarUpload" :data="{ where: 'student' }">
          <img v-if="imageUrl_2" :src="imageUrl_2" class="avatar">
          <img v-else-if="!imageUrl_2 && dataForm.cardBack" :src="cdn + dataForm.cardBack" alt="" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>

      </el-form-item>
      <el-form-item label="半身照" prop="photo">
        <!-- <el-input v-model="dataForm.photo" placeholder="上传成功返回随机数图片名" :disabled="true"></el-input> -->

        <el-upload class="avatar-uploader" :action="url + '/app/uploadFiles/photoUpload'" :show-file-list="false"
          :on-success="handleAvatarSuccess_3" :before-upload="beforeAvatarUpload" :data="{ where: 'student' }">
          <img v-if="imageUrl_3" :src="imageUrl_3" class="avatar">
          <img v-else-if="!imageUrl_3 && dataForm.photo" :src="cdn + dataForm.photo" alt="" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>

      </el-form-item>
      <el-form-item label="体检表照片" prop="tijianbiao">
        <!-- <el-input v-model="dataForm.tijianbiao" placeholder="上传成功返回随机数图片名" :disabled="true"></el-input> -->

        <el-upload class="avatar-uploader" :action="url + '/app/uploadFiles/photoUpload'" :show-file-list="false"
          :on-success="handleAvatarSuccess_4" :before-upload="beforeAvatarUpload" :data="{ where: 'student' }">
          <img v-if="imageUrl_4" :src="imageUrl_4" class="avatar">
          <img v-else-if="!imageUrl_4 && dataForm.tijianbiao" :src="cdn + dataForm.tijianbiao" alt="" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>

      </el-form-item>
      <el-form-item label="班型名称" prop="className">
        <el-input v-model="dataForm.className" placeholder="班型名称"></el-input>
      </el-form-item>
      <el-form-item label="报名时间" prop="addTime">
        <!-- <el-input v-model="dataForm.addTime" placeholder="报名时间"></el-input> -->
        <el-date-picker v-model="dataForm.addTime" type="date"
          :placeholder="(dataForm.addTime != '' ? '修改' : '选择') + '日期'" format="yyyy 年 MM 月 dd 日" value-format="timestamp">
        </el-date-picker>

      </el-form-item>
      <el-form-item label="学车进度" prop="progress">
        <!-- <el-input v-model="dataForm.progress" placeholder="学车进度{0为没有进度 11完成科一 21完成科二 31完成科三 41完成科四}"></el-input> -->
        <el-radio v-model="dataForm.progress" :label="0">未开始</el-radio>
        <el-radio v-model="dataForm.progress" :label="11">完成科目一</el-radio>
        <el-radio v-model="dataForm.progress" :label="21">完成科目二</el-radio>
        <el-radio v-model="dataForm.progress" :label="31">完成科目三</el-radio>
        <el-radio v-model="dataForm.progress" :label="41">完成科目四</el-radio>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import validate from "@/utils/validate";
export default {
  data() {
    return {
      imageUrl_1: '',
      imageUrl_2: '',
      imageUrl_3: '',
      imageUrl_4: '',
      url: window.SITE_CONFIG.baseUrl,
      cdn: 'https://wx.lejiatuan.cn/data/lejiatuan/super/student/',
      visible: false,
      options: [{
        value: 'A1',
        label: 'A1'
      }, {
        value: 'A2',
        label: 'A2'
      }, {
        value: 'A3',
        label: 'A3'
      }, {
        value: 'B1',
        label: 'B1'
      }, {
        value: 'B2',
        label: 'B2'
      }, {
        value: 'C1',
        label: 'C1'
      }, {
        value: 'C2',
        label: 'C2'
      }, {
        value: 'C3',
        label: 'C3'
      }, {
        value: 'C4',
        label: 'C4'
      }, {
        value: 'D',
        label: 'D'
      }, {
        value: 'E',
        label: 'E'
      }, {
        value: 'F',
        label: 'F'
      }, {
        value: 'M',
        label: 'M'
      }, {
        value: 'N',
        label: 'N'
      }, {
        value: 'P',
        label: 'P'
      }],
      schoolList: [],
      coachList: [],
      schoolId: null,
      dataForm: {
        stuId: 0,
        // userId: '',
        stuName: '',
        stuSex: '',
        stuPhone: '',
        classType: '',
        coachId: '',
        schoolId: '',
        schoolName: '',
        coachName: '',
        stuLevel: '',
        cardId: '',
        cardAd: '',
        cardFront: '',
        cardBack: '',
        photo: '',
        tijianbiao: '',
        className: '',
        addTime: '',

        progress: ''
      },

      dataRule: {
        // userId: [
        //   { required: true, message: '不能为空', trigger: 'blur' }
        // ],
        stuName: [{
          required: true,
          message: '学员姓名不能为空',
          trigger: 'blur'
        }],
        stuSex: [{
          required: true,
          message: '学员性别不能为空',
          trigger: 'blur'
        }],
        stuPhone: [{
          required: true,
          message: '学员电话不能为空',
          trigger: 'blur'
        }],
        classType: [{
          required: true,
          message: '报班车型不能为空',
          trigger: 'blur'
        }],
        coachId: [{
          required: true,
          message: '教练ID不能为空',
          trigger: 'blur'
        }],
        schoolName: [{
          required: true,
          message: '所属驾校不能为空',
          trigger: 'blur'
        }],
        coachName: [{
          required: true,
          message: '教练姓名不能为空',
          trigger: 'blur'
        }],
        stuLevel: [{
          required: true,
          message: '学员等级不能为空',
          trigger: 'blur'
        }],
        cardId: [{
          required: true,
          message: '身份证号不能为空',
          trigger: 'blur'
        },],
        cardAd: [{
          required: true,
          message: '学员身份证地址不能为空',
          trigger: 'blur'
        }],
        cardFront: [{
          required: true,
          message: '身份证正面照不能为空',
          trigger: 'blur'
        }],
        cardBack: [{
          required: true,
          message: '身份证背面照不能为空',
          trigger: 'blur'
        }],
        photo: [{
          required: false,
          message: '半身照不能为空',
          trigger: 'blur'
        }],
        tijianbiao: [{
          required: false,
          message: '体检表照片不能为空',
          trigger: 'blur'
        }],
        className: [{
          required: true,
          message: '班型名称不能为空',
          trigger: 'blur'
        }],
        addTime: [{
          required: true,
          message: '报名时间不能为空',
          trigger: 'blur'
        }],
        progress: [{
          required: true,
          message: '学车进度不能为空',
          trigger: 'blur'
        }]
      }
    }
  },
  mounted() {
    // this.getCoachList()
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
        if (data.data.code == 0) {
          this.schoolList = data.data.schoolEntities
        } else {
          // this.$message.error(data.msg)
        }
      })
    },
    schoolHandle(e) {
      this.dataForm.coachName = ''
      this.dataForm.coachId = ''
      for (let i = 0; i < this.schoolList.length; i++) {
        if (e == this.schoolList[i].schoolName) {
          this.schoolId = this.schoolList[i].schoolId
          this.dataForm.schoolId = this.schoolList[i].schoolId
          console.log(this.schoolId, this.schoolList[i].schoolId);
          this.getCoachList(this.schoolList[i].schoolId)
        }
      }
    },
    coachFocus() {
      if (this.schoolId != null) {
        this.getCoachList(this.schoolId)
      } else {
        this.$message('请先选择驾校')
      }
    },
    //拿教练列表
    getCoachList(schoolId) {
      this.$http({
        url: this.$http.adornUrl('/sys/coach/getlist'),
        method: 'get',
        params: this.$http.adornParams({ schoolId })
      }).then((data) => {
        console.log(data);
        if (data.data.code == 0) {
          this.coachList = data.data.wxCoachEntities
        } else {
          // this.$message.error(data.msg)
        }
      })
    },
    coachHandle(e) {
      for (let i = 0; i < this.coachList.length; i++) {
        if (e == this.coachList[i].coachName) {
          this.dataForm.coachId = this.coachList[i].coachId
        }
      }
    },
    handleAvatarSuccess_1(res, file) {
      if (res.code == 0) {
        this.dataForm.cardFront = res.fileName;
        this.imageUrl_1 = URL.createObjectURL(file.raw);
        this.$message({
          message: '上传成功',
          type: 'success'
        })
      } else {
        this.$message.error('上传失败' + ',错误信息:' + res.msg)
      }
    },
    handleAvatarSuccess_2(res, file) {
      if (res.code == 0) {
        this.imageUrl_2 = URL.createObjectURL(file.raw);
        this.dataForm.cardBack = res.fileName;
        this.$message({
          message: '上传成功',
          type: 'success'
        })
      } else {
        this.$message.error('上传失败' + ',错误信息:' + res.msg)
      }
    },
    handleAvatarSuccess_3(res, file) {
      if (res.code == 0) {
        this.imageUrl_3 = URL.createObjectURL(file.raw);
        this.dataForm.photo = res.fileName;
        this.$message({
          message: '上传成功',
          type: 'success'
        })
      } else {
        this.$message.error('上传失败' + ',错误信息:' + res.msg)
      }
    },
    handleAvatarSuccess_4(res, file) {
      if (res.code == 0) {
        this.imageUrl_4 = URL.createObjectURL(file.raw);
        this.dataForm.tijianbiao = res.fileName;
        this.$message({
          message: '上传成功',
          type: 'success'
        })
      } else {
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
      this.dataForm.stuId = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.stuId) {
          this.$http({
            url: this.$http.adornUrl(`/sys/student/info/${this.dataForm.stuId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.dataForm.userId = data.student.userId
              this.dataForm.stuName = data.student.stuName
              this.dataForm.stuSex = data.student.stuSex
              this.dataForm.stuPhone = data.student.stuPhone
              this.dataForm.classType = data.student.classType
              this.dataForm.coachId = data.student.coachId
              this.dataForm.coachName = data.student.coachName
              this.dataForm.stuLevel = data.student.stuLevel
              this.dataForm.cardId = data.student.cardId
              this.dataForm.cardAd = data.student.cardAd
              this.dataForm.cardFront = data.student.cardFront
              this.dataForm.cardBack = data.student.cardBack
              this.dataForm.photo = data.student.photo
              this.dataForm.tijianbiao = data.student.tijianbiao
              this.dataForm.className = data.student.className
              this.dataForm.addTime = data.student.addTimes
              this.dataForm.progress = data.student.progress
              this.dataForm.schoolId = data.student.schoolId
              this.schoolId = data.student.schoolId
              this.dataForm.schoolName = data.student.schoolName
              // this.dataForm.addTimes=data.student.addTimes
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
            url: this.$http.adornUrl(`/sys/student/${!this.dataForm.stuId ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'stuId': this.dataForm.stuId || undefined,
              'userId': this.dataForm.userId,
              'stuName': this.dataForm.stuName,
              'stuSex': this.dataForm.stuSex,
              'stuPhone': this.dataForm.stuPhone,
              'classType': this.dataForm.classType,
              'coachId': this.dataForm.coachId,
              'coachName': this.dataForm.coachName,
              'stuLevel': this.dataForm.stuLevel,
              'cardId': this.dataForm.cardId,
              'cardAd': this.dataForm.cardAd,
              'cardFront': this.dataForm.cardFront,
              'cardBack': this.dataForm.cardBack,
              'photo': this.dataForm.photo,
              'tijianbiao': this.dataForm.tijianbiao,
              'className': this.dataForm.className,
              'addTime': this.dataForm.addTime,
              'progress': this.dataForm.progress,
              'schoolId': this.dataForm.schoolId,
              'schoolName': this.dataForm.schoolName,
            })
          }).then(({
            data
          }) => {
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
