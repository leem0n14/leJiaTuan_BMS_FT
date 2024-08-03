<template>
  <el-dialog
    :title="!dataForm.groupId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="发起人id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="发起人id"></el-input>
    </el-form-item>
    <el-form-item label="商品id" prop="goodId">
      <el-input v-model="dataForm.goodId" placeholder="商品id"></el-input>
    </el-form-item>
    <el-form-item label="拼团所需人数" prop="requireNum">
      <el-input v-model="dataForm.requireNum" placeholder="拼团所需人数"></el-input>
    </el-form-item>
    <el-form-item label="当前拼团人数" prop="groupSum">
      <el-input v-model="dataForm.groupSum" placeholder="当前拼团人数"></el-input>
    </el-form-item>
    <el-form-item label="发起时间" prop="createtime">
      <el-input v-model="dataForm.createtime" placeholder="发起时间"></el-input>
    </el-form-item>
    <el-form-item label="拼团结束时间" prop="endtime">
      <el-input v-model="dataForm.endtime" placeholder="拼团结束时间"></el-input>
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
          groupId: 0,
          userId: '',
          goodId: '',
          requireNum: '',
          groupSum: '',
          createtime: '',
          endtime: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '发起人id不能为空', trigger: 'blur' }
          ],
          goodId: [
            { required: true, message: '商品id不能为空', trigger: 'blur' }
          ],
          requireNum: [
            { required: true, message: '拼团所需人数不能为空', trigger: 'blur' }
          ],
          groupSum: [
            { required: true, message: '当前拼团人数不能为空', trigger: 'blur' }
          ],
          createtime: [
            { required: true, message: '发起时间不能为空', trigger: 'blur' }
          ],
          endtime: [
            { required: true, message: '拼团结束时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.groupId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.groupId) {
            this.$http({
              url: this.$http.adornUrl(`/app/pintuan/info/${this.dataForm.groupId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.pintuan.userId
                this.dataForm.goodId = data.pintuan.goodId
                this.dataForm.requireNum = data.pintuan.requireNum
                this.dataForm.groupSum = data.pintuan.groupSum
                this.dataForm.createtime = data.pintuan.createtime
                this.dataForm.endtime = data.pintuan.endtime
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
              url: this.$http.adornUrl(`/app/pintuan/${!this.dataForm.groupId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'groupId': this.dataForm.groupId || undefined,
                'userId': this.dataForm.userId,
                'goodId': this.dataForm.goodId,
                'requireNum': this.dataForm.requireNum,
                'groupSum': this.dataForm.groupSum,
                'createtime': this.dataForm.createtime,
                'endtime': this.dataForm.endtime
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
