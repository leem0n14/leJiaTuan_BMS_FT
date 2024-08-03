<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="姓名" prop="name">
        <el-input v-model="dataForm.name" placeholder="姓名"></el-input>
      </el-form-item>
      <el-form-item label="电话" prop="phone">
        <el-input v-model="dataForm.phone" placeholder="电话"></el-input>
      </el-form-item>
      <el-form-item label="介绍" prop="introduce">
        <el-input v-model="dataForm.introduce" placeholder="介绍"></el-input>
      </el-form-item>
      <!-- <el-form-item label="提交时间" prop="addtime">
        <el-input v-model="dataForm.addtime" placeholder="
        
        提交时间" disabled></el-input>
      </el-form-item> -->
      <el-form-item label="状态" prop="statu">
        <!-- <el-input v-model="dataForm.statu" placeholder="状态"></el-input> -->
        <el-radio v-model="dataForm.statu" :label="2">不通过</el-radio>
        <el-radio v-model="dataForm.statu" :label="1">通过</el-radio>
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
        id: 0,
        name: '',
        phone: '',
        introduce: '',
        addtime: '',
        statu: ''
      },
      dataRule: {
        name: [
          { required: true, message: '姓名不能为空', trigger: 'blur' }
        ],
        phone: [
          { required: true, message: '电话不能为空', trigger: 'blur' }
        ],
        introduce: [
          { required: true, message: '介绍不能为空', trigger: 'blur' }
        ],
        // addtime: [
        //   { required: true, message: '提交时间不能为空', trigger: 'blur' }
        // ],
        statu: [
          { required: true, message: '状态不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/coachaudit/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.name = data.coachAudit.name
              this.dataForm.phone = data.coachAudit.phone
              this.dataForm.introduce = data.coachAudit.introduce
              this.dataForm.addtime = data.coachAudit.addtime
              this.dataForm.statu = data.coachAudit.statu
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
            url: this.$http.adornUrl(`/sys/coachaudit/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'name': this.dataForm.name,
              'phone': this.dataForm.phone,
              'introduce': this.dataForm.introduce,
              'addtime': this.dataForm.addtime,
              'statu': this.dataForm.statu
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
