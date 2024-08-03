<template>
  <el-dialog :title="!dataForm.userId ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="微信用户openID" prop="openId">
        <el-input v-model="dataForm.openId" placeholder="微信用户openID"></el-input>
      </el-form-item>
      <el-form-item label="电话" prop="phone">
        <el-input v-model="dataForm.phone" placeholder="电话"></el-input>
      </el-form-item>
      <el-form-item label="微信名" prop="name">
        <el-input v-model="dataForm.name" placeholder="微信名"></el-input>
      </el-form-item>
      <el-form-item label="微信头像" prop="head">
        <el-input v-model="dataForm.head" placeholder="微信头像"></el-input>
      </el-form-item>
      <el-form-item label="性别" prop="sex">
        <el-input v-model="dataForm.sex" placeholder="性别"></el-input>
      </el-form-item>
      <el-form-item label="地址" prop="address">
        <el-input v-model="dataForm.address" placeholder="地址"></el-input>
      </el-form-item>
      <el-form-item label="是否报考驾校{0 否 1 是}" prop="statu">
        <el-input v-model="dataForm.statu" placeholder="是否报考驾校{0 否 1 是}"></el-input>
      </el-form-item>
      <el-form-item label="报考驾驶证类型" prop="driverType">
        <el-input v-model="dataForm.driverType" placeholder="报考驾驶证类型"></el-input>
      </el-form-item>
      <el-form-item label="考驾照目的" prop="goal">
        <el-input v-model="dataForm.goal" placeholder="考驾照目的"></el-input>
      </el-form-item>
      <el-form-item label="积分" prop="point">
        <el-input v-model="dataForm.point" placeholder="积分"></el-input>
      </el-form-item>
      <el-form-item label="佣金" prop="money">
        <el-input v-model="dataForm.money" placeholder="佣金"></el-input>
      </el-form-item>
      <el-form-item label="身份{0普通用户 1学员 2教练}" prop="identity">
        <el-input v-model="dataForm.identity" placeholder="身份{0普通用户 1学员 2教练}"></el-input>
      </el-form-item>
      <el-form-item label="父id" prop="parentId">
        <el-input v-model="dataForm.parentId" placeholder="父id"></el-input>
      </el-form-item>
      <el-form-item label="创建时间" prop="createTime">
        <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
      </el-form-item>
      <el-form-item label="科目一练题" prop="questionNum1">
        <el-input v-model="dataForm.questionNum1" placeholder="科目一练题"></el-input>
      </el-form-item>
      <el-form-item label="科目四练题" prop="questionNum4">
        <el-input v-model="dataForm.questionNum4" placeholder="科目四练题"></el-input>
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
      visible: false,
      dataForm: {
        userId: 0,
        openId: '',
        phone: '',
        name: '',
        head: '',
        sex: '',
        address: '',
        statu: '',
        driverType: '',
        goal: '',
        point: '',
        money: '',
        identity: '',
        parentId: '',
        createTime: '',
        questionNum1: '',
        questionNum4: ''
      },
      dataRule: {
        openId: [
          { required: true, message: '微信用户openID不能为空', trigger: 'blur' }
        ],
        phone: [
          { required: true, message: '电话不能为空', trigger: 'blur' }
        ],
        name: [
          { required: true, message: '微信名不能为空', trigger: 'blur' }
        ],
        head: [
          { required: true, message: '微信头像不能为空', trigger: 'blur' }
        ],
        sex: [
          { required: true, message: '性别不能为空', trigger: 'blur' }
        ],
        address: [
          { required: true, message: '地址不能为空', trigger: 'blur' }
        ],
        statu: [
          { required: true, message: '是否报考驾校{0 否 1 是}不能为空', trigger: 'blur' }
        ],
        driverType: [
          { required: true, message: '报考驾驶证类型不能为空', trigger: 'blur' }
        ],
        goal: [
          { required: true, message: '考驾照目的不能为空', trigger: 'blur' }
        ],
        point: [
          { required: true, message: '积分不能为空', trigger: 'blur' }
        ],
        money: [
          { required: true, message: '佣金不能为空', trigger: 'blur' }
        ],
        identity: [
          { required: true, message: '身份{0普通用户 1学员 2教练}不能为空', trigger: 'blur' }
        ],
        parentId: [
          { required: true, message: '父id不能为空', trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: '创建时间不能为空', trigger: 'blur' }
        ],
        questionNum1: [
          { required: true, message: '科目一练题不能为空', trigger: 'blur' }
        ],
        questionNum4: [
          { required: true, message: '科目四练题不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    init(id) {
      this.dataForm.userId = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.userId) {
          this.$http({
            url: this.$http.adornUrl(`/sys/wxuser/info/${this.dataForm.userId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.openId = data.user.openId
              this.dataForm.phone = data.user.phone
              this.dataForm.name = data.user.name
              this.dataForm.head = data.user.head
              this.dataForm.sex = data.user.sex
              this.dataForm.address = data.user.address
              this.dataForm.statu = data.user.statu
              this.dataForm.driverType = data.user.driverType
              this.dataForm.goal = data.user.goal
              this.dataForm.point = data.user.point
              this.dataForm.money = data.user.money
              this.dataForm.identity = data.user.identity
              this.dataForm.parentId = data.user.parentId
              this.dataForm.createTime = data.user.createTime
              this.dataForm.questionNum1 = data.user.questionNum1
              this.dataForm.questionNum4 = data.user.questionNum4
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
            url: this.$http.adornUrl(`/sys/wxuser/${!this.dataForm.userId ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'userId': this.dataForm.userId || undefined,
              'openId': this.dataForm.openId,
              'phone': this.dataForm.phone,
              'name': this.dataForm.name,
              'head': this.dataForm.head,
              'sex': this.dataForm.sex,
              'address': this.dataForm.address,
              'statu': this.dataForm.statu,
              'driverType': this.dataForm.driverType,
              'goal': this.dataForm.goal,
              'point': this.dataForm.point,
              'money': this.dataForm.money,
              'identity': this.dataForm.identity,
              'parentId': this.dataForm.parentId,
              'createTime': this.dataForm.createTime,
              'questionNum1': this.dataForm.questionNum1,
              'questionNum4': this.dataForm.questionNum4
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
