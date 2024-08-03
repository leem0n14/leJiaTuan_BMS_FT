<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="教练ID" prop="coachId">
        <el-input v-model="dataForm.coachId" placeholder="教练ID" disabled></el-input>
      </el-form-item>
      <el-form-item label="教练姓名" prop="coachName">
        <el-input v-model="dataForm.coachName" placeholder="教练姓名" disabled></el-input>
      </el-form-item>
      <el-form-item label="签到时间" prop="registerTime">
        <el-input v-model="dataForm.registerTime" placeholder="签到时间" disabled></el-input>
      </el-form-item>
      <el-form-item label="打卡地点" prop="site">
        <el-input v-model="dataForm.site" placeholder="打卡地点" disabled></el-input>
      </el-form-item>
      <el-form-item label="签到状态" prop="statu">
        <!-- <el-input v-model="dataForm.statu" placeholder="签到状态"></el-input> -->
        <el-radio-group v-model="dataForm.statu">
          <el-radio label="准时">准时</el-radio>
          <el-radio label="迟到">迟到</el-radio>
        </el-radio-group>
        <!-- <el-radio v-model="dataForm.statu" label="已签退">已签退</el-radio> -->
        <!-- <el-radio v-model="dataForm.statu" label="早退">早退</el-radio> -->
      </el-form-item>
      <el-form-item label="打卡图片" prop="rePhoto">
        <el-input v-model="dataForm.rePhoto" placeholder="打卡图片" disabled></el-input>
      </el-form-item>
      <el-form-item label="经度" prop="longitude">
        <el-input v-model="dataForm.longitude" placeholder="经度" disabled></el-input>
      </el-form-item>
      <el-form-item label="纬度" prop="latitude">
        <el-input v-model="dataForm.latitude" placeholder="纬度" disabled></el-input>
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
        coachId: '',
        coachName: '',
        registerTime: '',
        site: '',
        statu: '',
        rePhoto: '',
        longitude: '',
        latitude: ''
      },
      dataRule: {
        coachId: [
          { required: true, message: '教练ID不能为空', trigger: 'blur' }
        ],
        coachName: [
          { required: true, message: '教练姓名不能为空', trigger: 'blur' }
        ],
        registerTime: [
          { required: true, message: '签到时间不能为空', trigger: 'blur' }
        ],
        site: [
          { required: true, message: '打卡地点不能为空', trigger: 'blur' }
        ],
        statu: [
          { required: true, message: '签到状态不能为空', trigger: 'blur' }
        ],
        rePhoto: [
          { required: true, message: '打卡图片不能为空', trigger: 'blur' }
        ],
        longitude: [
          { required: true, message: '经度不能为空', trigger: 'blur' }
        ],
        latitude: [
          { required: true, message: '纬度不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/sys/coachregister/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.coachId = data.coachRegister.coachId
              this.dataForm.coachName = data.coachRegister.coachName
              this.dataForm.registerTime = data.coachRegister.registerTime
              this.dataForm.site = data.coachRegister.site
              this.dataForm.statu = data.coachRegister.statu
              this.dataForm.rePhoto = data.coachRegister.rePhoto
              this.dataForm.longitude = data.coachRegister.longitude
              this.dataForm.latitude = data.coachRegister.latitude
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
            url: this.$http.adornUrl(`/sys/coachregister/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'coachId': this.dataForm.coachId,
              'coachName': this.dataForm.coachName,
              'registerTime': this.dataForm.registerTime,
              'site': this.dataForm.site,
              'statu': this.dataForm.statu,
              'rePhoto': this.dataForm.rePhoto,
              'longitude': this.dataForm.longitude,
              'latitude': this.dataForm.latitude
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
