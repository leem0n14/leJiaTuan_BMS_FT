<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="车牌号" prop="carNum">
      <el-input v-model="dataForm.carNum" placeholder="车牌号"></el-input>
    </el-form-item>
    <el-form-item label="教练id" prop="coachId">
      <el-input v-model="dataForm.coachId" placeholder="教练id"></el-input>
    </el-form-item>
    <el-form-item label="教练名字" prop="coachName">
      <el-input v-model="dataForm.coachName" placeholder="教练名字"></el-input>
    </el-form-item>
    <el-form-item label="加油金额" prop="amount">
      <el-input v-model="dataForm.amount" placeholder="加油金额"></el-input>
    </el-form-item>
    <el-form-item label="小票照片" prop="receipts">
      <el-input v-model="dataForm.receipts" placeholder="小票照片"></el-input>
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
          carNum: '',
          coachId: '',
          coachName: '',
          amount: '',
          receipts: ''
        },
        dataRule: {
          carNum: [
            { required: true, message: '车牌号不能为空', trigger: 'blur' }
          ],
          coachId: [
            { required: true, message: '教练id不能为空', trigger: 'blur' }
          ],
          coachName: [
            { required: true, message: '教练名字不能为空', trigger: 'blur' }
          ],
          amount: [
            { required: true, message: '加油金额不能为空', trigger: 'blur' }
          ],
          receipts: [
            { required: true, message: '小票照片不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/refuel/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.carNum = data.refuel.carNum
                this.dataForm.coachId = data.refuel.coachId
                this.dataForm.coachName = data.refuel.coachName
                this.dataForm.amount = data.refuel.amount
                this.dataForm.receipts = data.refuel.receipts
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
              url: this.$http.adornUrl(`/app/refuel/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'carNum': this.dataForm.carNum,
                'coachId': this.dataForm.coachId,
                'coachName': this.dataForm.coachName,
                'amount': this.dataForm.amount,
                'receipts': this.dataForm.receipts
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
