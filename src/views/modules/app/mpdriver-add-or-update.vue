<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="" prop="userId">
        <el-input v-model="dataForm.userId" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="姓名" prop="mpName">
        <el-input v-model="dataForm.mpName" placeholder="姓名"></el-input>
      </el-form-item>
      <el-form-item label="教练id" prop="coachId">
        <el-input v-model="dataForm.coachId" placeholder="教练id"></el-input>
      </el-form-item>
      <el-form-item label="电话" prop="phone">
        <el-input v-model="dataForm.phone" placeholder="电话"></el-input>
      </el-form-item>
      <el-form-item label="练车类型" prop="carTypes">
        <el-input v-model="dataForm.carTypes" placeholder="练车类型"></el-input>
      </el-form-item>
      <el-form-item label="接送地址" prop="mpAdd">
        <el-input v-model="dataForm.mpAdd" placeholder="接送地址"></el-input>
      </el-form-item>
      <el-form-item label="科目名称" prop="object">
        <el-input v-model="dataForm.object" placeholder="科目名称"></el-input>
      </el-form-item>
      <el-form-item label="练车时间段" prop="carTime">
        <el-input v-model="dataForm.carTime" placeholder="练车时间段"></el-input>
      </el-form-item>
      <el-form-item label="是否需要接送" prop="jiesongStatu">
        <el-input v-model="dataForm.jiesongStatu" placeholder="是否需要接送{0 不需要 1需要}"></el-input>
      </el-form-item>
      <el-form-item label="预约是否通过" prop="statu">
        <el-input v-model="dataForm.statu" placeholder="预约是否通过{0为审核中，1为同意，2为不同意}"></el-input>
      </el-form-item>
      <el-form-item label="不同意原因" prop="reason">
        <el-input v-model="dataForm.reason" placeholder="不同意原因"></el-input>
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
        userId: '',
        mpName: '',
        coachId: '',
        phone: '',
        carTypes: '',
        mpAdd: '',
        object: '',
        carTime: '',
        jiesongStatu: '',
        statu: '',
        reason: ''
      },
      dataRule: {
        userId: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        mpName: [
          { required: true, message: '姓名不能为空', trigger: 'blur' }
        ],
        coachId: [
          { required: true, message: '教练id不能为空', trigger: 'blur' }
        ],
        phone: [
          { required: true, message: '电话不能为空', trigger: 'blur' }
        ],
        carTypes: [
          { required: true, message: '练车类型不能为空', trigger: 'blur' }
        ],
        mpAdd: [
          { required: true, message: '接送地址不能为空', trigger: 'blur' }
        ],
        object: [
          { required: true, message: '科目名称不能为空', trigger: 'blur' }
        ],
        carTime: [
          { required: true, message: '练车时间段不能为空', trigger: 'blur' }
        ],
        jiesongStatu: [
          { required: true, message: '是否需要接送{0 不需要 1需要}不能为空', trigger: 'blur' }
        ],
        statu: [
          { required: true, message: '预约是否通过{0为审核中，1为同意，2为不同意}不能为空', trigger: 'blur' }
        ],
        reason: [
          { required: true, message: '不同意原因不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/app/mpdriver/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.userId = data.mpDriver.userId
              this.dataForm.mpName = data.mpDriver.mpName
              this.dataForm.coachId = data.mpDriver.coachId
              this.dataForm.phone = data.mpDriver.phone
              this.dataForm.carTypes = data.mpDriver.carTypes
              this.dataForm.mpAdd = data.mpDriver.mpAdd
              this.dataForm.object = data.mpDriver.object
              this.dataForm.carTime = data.mpDriver.carTime
              this.dataForm.jiesongStatu = data.mpDriver.jiesongStatu
              this.dataForm.statu = data.mpDriver.statu
              this.dataForm.reason = data.mpDriver.reason
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
            url: this.$http.adornUrl(`/sys/mpdriver/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'userId': this.dataForm.userId,
              'mpName': this.dataForm.mpName,
              'coachId': this.dataForm.coachId,
              'phone': this.dataForm.phone,
              'carTypes': this.dataForm.carTypes,
              'mpAdd': this.dataForm.mpAdd,
              'object': this.dataForm.object,
              'carTime': this.dataForm.carTime,
              'jiesongStatu': this.dataForm.jiesongStatu,
              'statu': this.dataForm.statu,
              'reason': this.dataForm.reason
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
