<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="订单ID" prop="orderId">
      <el-input v-model="dataForm.orderId" placeholder="订单ID"></el-input>
    </el-form-item>
    <el-form-item label="发起人ID" prop="fquserId">
      <el-input v-model="dataForm.fquserId" placeholder="发起人ID"></el-input>
    </el-form-item>
    <el-form-item label="分销用户名" prop="fxName">
      <el-input v-model="dataForm.fxName" placeholder="分销用户名"></el-input>
    </el-form-item>
    <el-form-item label="分销用户ID" prop="fxuserId">
      <el-input v-model="dataForm.fxuserId" placeholder="分销用户ID"></el-input>
    </el-form-item>
    <el-form-item label="佣金" prop="money">
      <el-input v-model="dataForm.money" placeholder="佣金"></el-input>
    </el-form-item>
    <el-form-item label="佣金级别" prop="level">
      <el-input v-model="dataForm.level" placeholder="佣金级别"></el-input>
    </el-form-item>
    <el-form-item label="收入佣金时间" prop="time">
      <el-input v-model="dataForm.time" placeholder="收入佣金时间"></el-input>
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
          orderId: '',
          fquserId: '',
          fxName: '',
          fxuserId: '',
          money: '',
          level: '',
          time: ''
        },
        dataRule: {
          orderId: [
            { required: true, message: '订单ID不能为空', trigger: 'blur' }
          ],
          fquserId: [
            { required: true, message: '发起人ID不能为空', trigger: 'blur' }
          ],
          fxName: [
            { required: true, message: '分销用户名不能为空', trigger: 'blur' }
          ],
          fxuserId: [
            { required: true, message: '分销用户ID不能为空', trigger: 'blur' }
          ],
          money: [
            { required: true, message: '佣金不能为空', trigger: 'blur' }
          ],
          level: [
            { required: true, message: '佣金级别不能为空', trigger: 'blur' }
          ],
          time: [
            { required: true, message: '收入佣金时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/commissionjl/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.orderId = data.commissionJl.orderId
                this.dataForm.fquserId = data.commissionJl.fquserId
                this.dataForm.fxName = data.commissionJl.fxName
                this.dataForm.fxuserId = data.commissionJl.fxuserId
                this.dataForm.money = data.commissionJl.money
                this.dataForm.level = data.commissionJl.level
                this.dataForm.time = data.commissionJl.time
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
              url: this.$http.adornUrl(`/app/commissionjl/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'orderId': this.dataForm.orderId,
                'fquserId': this.dataForm.fquserId,
                'fxName': this.dataForm.fxName,
                'fxuserId': this.dataForm.fxuserId,
                'money': this.dataForm.money,
                'level': this.dataForm.level,
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
