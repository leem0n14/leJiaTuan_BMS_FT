<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="用户姓名" prop="userName">
      <el-input v-model="dataForm.userName" placeholder="用户姓名"></el-input>
    </el-form-item>
    <el-form-item label="提现金额" prop="sum">
      <el-input v-model="dataForm.sum" placeholder="提现金额"></el-input>
    </el-form-item>
    <el-form-item label="提现方式" prop="cashMethod">
      <el-input v-model="dataForm.cashMethod" placeholder="提现方式"></el-input>
    </el-form-item>
    <el-form-item label="卡号{银行卡或支付宝号}" prop="cardNumber">
      <el-input v-model="dataForm.cardNumber" placeholder="卡号{银行卡或支付宝号}"></el-input>
    </el-form-item>
    <el-form-item label="提现状态{0 审核中 1 通过  2 不通过}" prop="statu">
      <el-input v-model="dataForm.statu" placeholder="提现状态{0 审核中 1 通过  2 不通过}"></el-input>
    </el-form-item>
    <el-form-item label="不通过原因" prop="noReason">
      <el-input v-model="dataForm.noReason" placeholder="不通过原因"></el-input>
    </el-form-item>
    <el-form-item label="是否结算佣金{0 未结算 1已结算}" prop="jsStatu">
      <el-input v-model="dataForm.jsStatu" placeholder="是否结算佣金{0 未结算 1已结算}"></el-input>
    </el-form-item>
    <el-form-item label="提现发起时间" prop="time">
      <el-input v-model="dataForm.time" placeholder="提现发起时间"></el-input>
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
          userName: '',
          sum: '',
          cashMethod: '',
          cardNumber: '',
          statu: '',
          noReason: '',
          jsStatu: '',
          time: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          userName: [
            { required: true, message: '用户姓名不能为空', trigger: 'blur' }
          ],
          sum: [
            { required: true, message: '提现金额不能为空', trigger: 'blur' }
          ],
          cashMethod: [
            { required: true, message: '提现方式不能为空', trigger: 'blur' }
          ],
          cardNumber: [
            { required: true, message: '卡号{银行卡或支付宝号}不能为空', trigger: 'blur' }
          ],
          statu: [
            { required: true, message: '提现状态{0 审核中 1 通过  2 不通过}不能为空', trigger: 'blur' }
          ],
          noReason: [
            { required: true, message: '不通过原因不能为空', trigger: 'blur' }
          ],
          jsStatu: [
            { required: true, message: '是否结算佣金{0 未结算 1已结算}不能为空', trigger: 'blur' }
          ],
          time: [
            { required: true, message: '提现发起时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/cash/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.cash.userId
                this.dataForm.userName = data.cash.userName
                this.dataForm.sum = data.cash.sum
                this.dataForm.cashMethod = data.cash.cashMethod
                this.dataForm.cardNumber = data.cash.cardNumber
                this.dataForm.statu = data.cash.statu
                this.dataForm.noReason = data.cash.noReason
                this.dataForm.jsStatu = data.cash.jsStatu
                this.dataForm.time = data.cash.time
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
              url: this.$http.adornUrl(`/app/cash/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'userName': this.dataForm.userName,
                'sum': this.dataForm.sum,
                'cashMethod': this.dataForm.cashMethod,
                'cardNumber': this.dataForm.cardNumber,
                'statu': this.dataForm.statu,
                'noReason': this.dataForm.noReason,
                'jsStatu': this.dataForm.jsStatu,
                'time': this.dataForm.time
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
