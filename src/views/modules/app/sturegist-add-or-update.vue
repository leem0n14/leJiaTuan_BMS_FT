<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="userId">
      <el-input v-model="dataForm.userId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="姓名" prop="stuName">
      <el-input v-model="dataForm.stuName" placeholder="姓名"></el-input>
    </el-form-item>
    <el-form-item label="电话" prop="stuPhone">
      <el-input v-model="dataForm.stuPhone" placeholder="电话"></el-input>
    </el-form-item>
    <el-form-item label="驾校id" prop="schoolId">
      <el-input v-model="dataForm.schoolId" placeholder="驾校id"></el-input>
    </el-form-item>
    <el-form-item label="报名驾校" prop="schoolName">
      <el-input v-model="dataForm.schoolName" placeholder="报名驾校"></el-input>
    </el-form-item>
    <el-form-item label="性别" prop="stuSex">
      <el-input v-model="dataForm.stuSex" placeholder="性别"></el-input>
    </el-form-item>
    <el-form-item label="报班类型" prop="classType">
      <el-input v-model="dataForm.classType" placeholder="报班类型"></el-input>
    </el-form-item>
    <el-form-item label="是否报名成功" prop="statu">
      <el-input v-model="dataForm.statu" placeholder="是否报名成功"></el-input>
    </el-form-item>
    <el-form-item label="不通过原因" prop="noReason">
      <el-input v-model="dataForm.noReason" placeholder="不通过原因"></el-input>
    </el-form-item>
    <el-form-item label="身份证号" prop="cardId">
      <el-input v-model="dataForm.cardId" placeholder="身份证号"></el-input>
    </el-form-item>
    <el-form-item label="身份证正面照" prop="cardFront">
      <el-input v-model="dataForm.cardFront" placeholder="身份证正面照"></el-input>
    </el-form-item>
    <el-form-item label="身份证背面照" prop="cardBack">
      <el-input v-model="dataForm.cardBack" placeholder="身份证背面照"></el-input>
    </el-form-item>
    <el-form-item label="上半身照" prop="photo">
      <el-input v-model="dataForm.photo" placeholder="上半身照"></el-input>
    </el-form-item>
    <el-form-item label="体检表照片" prop="tijianbiao">
      <el-input v-model="dataForm.tijianbiao" placeholder="体检表照片"></el-input>
    </el-form-item>
    <el-form-item label="报名班型" prop="goodName">
      <el-input v-model="dataForm.goodName" placeholder="报名班型"></el-input>
    </el-form-item>
    <el-form-item label="报名时间" prop="addTime">
      <el-input v-model="dataForm.addTime" placeholder="报名时间"></el-input>
    </el-form-item>
    <el-form-item label="身份证地址" prop="cardAd">
      <el-input v-model="dataForm.cardAd" placeholder="身份证地址"></el-input>
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
          userId: '',
          stuName: '',
          stuPhone: '',
          schoolId: '',
          schoolName: '',
          stuSex: '',
          classType: '',
          statu: '',
          noReason: '',
          cardId: '',
          cardFront: '',
          cardBack: '',
          photo: '',
          tijianbiao: '',
          goodName: '',
          addTime: '',
          cardAd: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          stuName: [
            { required: true, message: '姓名不能为空', trigger: 'blur' }
          ],
          stuPhone: [
            { required: true, message: '电话不能为空', trigger: 'blur' }
          ],
          schoolId: [
            { required: true, message: '驾校id不能为空', trigger: 'blur' }
          ],
          schoolName: [
            { required: true, message: '报名驾校不能为空', trigger: 'blur' }
          ],
          stuSex: [
            { required: true, message: '性别不能为空', trigger: 'blur' }
          ],
          classType: [
            { required: true, message: '报班类型不能为空', trigger: 'blur' }
          ],
          statu: [
            { required: true, message: '是否报名成功不能为空', trigger: 'blur' }
          ],
          noReason: [
            { required: true, message: '不通过原因不能为空', trigger: 'blur' }
          ],
          cardId: [
            { required: true, message: '身份证号不能为空', trigger: 'blur' }
          ],
          cardFront: [
            { required: true, message: '身份证正面照不能为空', trigger: 'blur' }
          ],
          cardBack: [
            { required: true, message: '身份证背面照不能为空', trigger: 'blur' }
          ],
          photo: [
            { required: true, message: '上半身照不能为空', trigger: 'blur' }
          ],
          tijianbiao: [
            { required: true, message: '体检表照片不能为空', trigger: 'blur' }
          ],
          goodName: [
            { required: true, message: '报名班型不能为空', trigger: 'blur' }
          ],
          addTime: [
            { required: true, message: '报名时间不能为空', trigger: 'blur' }
          ],
          cardAd: [
            { required: true, message: '身份证地址不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/sys/sturegist/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.stuRegist.userId
                this.dataForm.stuName = data.stuRegist.stuName
                this.dataForm.stuPhone = data.stuRegist.stuPhone
                this.dataForm.schoolId = data.stuRegist.schoolId
                this.dataForm.schoolName = data.stuRegist.schoolName
                this.dataForm.stuSex = data.stuRegist.stuSex
                this.dataForm.classType = data.stuRegist.classType
                this.dataForm.statu = data.stuRegist.statu
                this.dataForm.noReason = data.stuRegist.noReason
                this.dataForm.cardId = data.stuRegist.cardId
                this.dataForm.cardFront = data.stuRegist.cardFront
                this.dataForm.cardBack = data.stuRegist.cardBack
                this.dataForm.photo = data.stuRegist.photo
                this.dataForm.tijianbiao = data.stuRegist.tijianbiao
                this.dataForm.goodName = data.stuRegist.goodName
                this.dataForm.addTime = data.stuRegist.addTime
                this.dataForm.cardAd = data.stuRegist.cardAd
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
              url: this.$http.adornUrl(`/sys/sturegist/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'stuName': this.dataForm.stuName,
                'stuPhone': this.dataForm.stuPhone,
                'schoolId': this.dataForm.schoolId,
                'schoolName': this.dataForm.schoolName,
                'stuSex': this.dataForm.stuSex,
                'classType': this.dataForm.classType,
                'statu': this.dataForm.statu,
                'noReason': this.dataForm.noReason,
                'cardId': this.dataForm.cardId,
                'cardFront': this.dataForm.cardFront,
                'cardBack': this.dataForm.cardBack,
                'photo': this.dataForm.photo,
                'tijianbiao': this.dataForm.tijianbiao,
                'goodName': this.dataForm.goodName,
                'addTime': this.dataForm.addTime,
                'cardAd': this.dataForm.cardAd
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
