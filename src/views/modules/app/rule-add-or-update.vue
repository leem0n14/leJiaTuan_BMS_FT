<template>
  <el-dialog
    :title="!dataForm.ruleId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="名称"></el-input>
    </el-form-item>
    <el-form-item label="优惠券类型，0-全场可用、1-指定驾校、2-指定教练" prop="type">
      <el-input v-model="dataForm.type" placeholder="优惠券类型，0-全场可用、1-指定驾校、2-指定教练"></el-input>
    </el-form-item>
    <el-form-item label="指定使用id，全场为null" prop="appointId">
      <el-input v-model="dataForm.appointId" placeholder="指定使用id，全场为null"></el-input>
    </el-form-item>
    <el-form-item label="规则内容" prop="ruleContent">
      <el-input v-model="dataForm.ruleContent" placeholder="规则内容"></el-input>
    </el-form-item>
    <el-form-item label="使用门槛" prop="threshold">
      <el-input v-model="dataForm.threshold" placeholder="使用门槛"></el-input>
    </el-form-item>
    <el-form-item label="优惠金额" prop="amount">
      <el-input v-model="dataForm.amount" placeholder="优惠金额"></el-input>
    </el-form-item>
    <el-form-item label="每个用户可以领取的数量" prop="receiveCount">
      <el-input v-model="dataForm.receiveCount" placeholder="每个用户可以领取的数量"></el-input>
    </el-form-item>
    <el-form-item label="领取开始时间" prop="receiveStartedAt">
      <el-input v-model="dataForm.receiveStartedAt" placeholder="领取开始时间"></el-input>
    </el-form-item>
    <el-form-item label="领取结束时间" prop="receiveEndedAt">
      <el-input v-model="dataForm.receiveEndedAt" placeholder="领取结束时间"></el-input>
    </el-form-item>
    <el-form-item label="使用开始时间" prop="useStartedAt">
      <el-input v-model="dataForm.useStartedAt" placeholder="使用开始时间"></el-input>
    </el-form-item>
    <el-form-item label="使用结束时间" prop="useEndedAt">
      <el-input v-model="dataForm.useEndedAt" placeholder="使用结束时间"></el-input>
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
          ruleId: 0,
          name: '',
          type: '',
          appointId: '',
          ruleContent: '',
          threshold: '',
          amount: '',
          receiveCount: '',
          receiveStartedAt: '',
          receiveEndedAt: '',
          useStartedAt: '',
          useEndedAt: ''
        },
        dataRule: {
          name: [
            { required: true, message: '名称不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '优惠券类型，0-全场可用、1-指定驾校、2-指定教练不能为空', trigger: 'blur' }
          ],
          appointId: [
            { required: true, message: '指定使用id，全场为null不能为空', trigger: 'blur' }
          ],
          ruleContent: [
            { required: true, message: '规则内容不能为空', trigger: 'blur' }
          ],
          threshold: [
            { required: true, message: '使用门槛不能为空', trigger: 'blur' }
          ],
          amount: [
            { required: true, message: '优惠金额不能为空', trigger: 'blur' }
          ],
          receiveCount: [
            { required: true, message: '每个用户可以领取的数量不能为空', trigger: 'blur' }
          ],
          receiveStartedAt: [
            { required: true, message: '领取开始时间不能为空', trigger: 'blur' }
          ],
          receiveEndedAt: [
            { required: true, message: '领取结束时间不能为空', trigger: 'blur' }
          ],
          useStartedAt: [
            { required: true, message: '使用开始时间不能为空', trigger: 'blur' }
          ],
          useEndedAt: [
            { required: true, message: '使用结束时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.ruleId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.ruleId) {
            this.$http({
              url: this.$http.adornUrl(`/app/rule/info/${this.dataForm.ruleId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.rule.name
                this.dataForm.type = data.rule.type
                this.dataForm.appointId = data.rule.appointId
                this.dataForm.ruleContent = data.rule.ruleContent
                this.dataForm.threshold = data.rule.threshold
                this.dataForm.amount = data.rule.amount
                this.dataForm.receiveCount = data.rule.receiveCount
                this.dataForm.receiveStartedAt = data.rule.receiveStartedAt
                this.dataForm.receiveEndedAt = data.rule.receiveEndedAt
                this.dataForm.useStartedAt = data.rule.useStartedAt
                this.dataForm.useEndedAt = data.rule.useEndedAt
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
              url: this.$http.adornUrl(`/app/rule/${!this.dataForm.ruleId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'ruleId': this.dataForm.ruleId || undefined,
                'name': this.dataForm.name,
                'type': this.dataForm.type,
                'appointId': this.dataForm.appointId,
                'ruleContent': this.dataForm.ruleContent,
                'threshold': this.dataForm.threshold,
                'amount': this.dataForm.amount,
                'receiveCount': this.dataForm.receiveCount,
                'receiveStartedAt': this.dataForm.receiveStartedAt,
                'receiveEndedAt': this.dataForm.receiveEndedAt,
                'useStartedAt': this.dataForm.useStartedAt,
                'useEndedAt': this.dataForm.useEndedAt
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
