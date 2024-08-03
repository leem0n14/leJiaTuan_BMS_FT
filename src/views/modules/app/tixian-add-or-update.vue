<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="userId">
      <el-input v-model="dataForm.userId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="真实姓名" prop="name">
      <el-input v-model="dataForm.name" placeholder="真实姓名"></el-input>
    </el-form-item>
    <el-form-item label="银行名字" prop="cardName">
      <el-input v-model="dataForm.cardName" placeholder="银行名字"></el-input>
    </el-form-item>
    <el-form-item label="提现银行卡账号" prop="cardId">
      <el-input v-model="dataForm.cardId" placeholder="提现银行卡账号"></el-input>
    </el-form-item>
    <el-form-item label="提现金额" prop="money">
      <el-input v-model="dataForm.money" placeholder="提现金额"></el-input>
    </el-form-item>
    <el-form-item label="提现时间" prop="time">
      <el-input v-model="dataForm.time" placeholder="提现时间"></el-input>
    </el-form-item>
    <el-form-item label="提现状态{0 提现中 1提现成功 2提现失败}" prop="statu">
      <el-input v-model="dataForm.statu" placeholder="提现状态{0 提现中 1提现成功 2提现失败}"></el-input>
    </el-form-item>
    <el-form-item label="提现失败原因" prop="reason">
      <el-input v-model="dataForm.reason" placeholder="提现失败原因"></el-input>
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
          name: '',
          cardName: '',
          cardId: '',
          money: '',
          time: '',
          statu: '',
          reason: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '真实姓名不能为空', trigger: 'blur' }
          ],
          cardName: [
            { required: true, message: '银行名字不能为空', trigger: 'blur' }
          ],
          cardId: [
            { required: true, message: '提现银行卡账号不能为空', trigger: 'blur' }
          ],
          money: [
            { required: true, message: '提现金额不能为空', trigger: 'blur' }
          ],
          time: [
            { required: true, message: '提现时间不能为空', trigger: 'blur' }
          ],
          statu: [
            { required: true, message: '提现状态{0 提现中 1提现成功 2提现失败}不能为空', trigger: 'blur' }
          ],
          reason: [
            { required: true, message: '提现失败原因不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/sys/tixian/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.tixian.userId
                this.dataForm.name = data.tixian.name
                this.dataForm.cardName = data.tixian.cardName
                this.dataForm.cardId = data.tixian.cardId
                this.dataForm.money = data.tixian.money
                this.dataForm.time = data.tixian.time
                this.dataForm.statu = data.tixian.statu
                this.dataForm.reason = data.tixian.reason
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
              url: this.$http.adornUrl(`/sys/tixian/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'name': this.dataForm.name,
                'cardName': this.dataForm.cardName,
                'cardId': this.dataForm.cardId,
                'money': this.dataForm.money,
                'time': this.dataForm.time,
                'statu': this.dataForm.statu,
                'reason': this.dataForm.reason
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
