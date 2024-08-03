<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="预约开始时间" prop="startTime">
        <!-- <el-input v-model="dataForm.startTime" placeholder="预约开始时间"></el-input> -->
        <el-time-picker arrow-control v-model="dataForm.startTime" placeholder="预约开始时间" value-format="HH:mm:ss">
        </el-time-picker>
      </el-form-item>
      <el-form-item label="预约结束时间" prop="endTime">
        <!-- <el-input v-model="dataForm.endTime" placeholder="预约结束时间"></el-input> -->
        <el-time-picker arrow-control v-model="dataForm.endTime" placeholder="预约开始时间" value-format="HH:mm:ss">
        </el-time-picker>
      </el-form-item>
      <el-form-item label="拼车每公里价格(元)" prop="carPrice">
        <el-input v-model="dataForm.carPrice" placeholder="拼车每公里价格"></el-input>
      </el-form-item>
      <el-form-item label="一级佣金(元)" prop="money1">
        <el-input v-model="dataForm.money1" placeholder="一级佣金"></el-input>
      </el-form-item>
      <el-form-item label="二级佣金(元)" prop="money2">
        <el-input v-model="dataForm.money2" placeholder="二级佣金"></el-input>
      </el-form-item>
      <el-form-item label="一天签到积分" prop="point1">
        <el-input v-model="dataForm.point1" placeholder="一天签到积分"></el-input>
      </el-form-item>
      <el-form-item label="七天签到积分" prop="point2">
        <el-input v-model="dataForm.point2" placeholder="七天签到积分"></el-input>
      </el-form-item>
      <el-form-item label="预约考试规则" prop="examRule">
        <el-input v-model="dataForm.examRule" placeholder="预约考试规则" type="textarea"></el-input>
      </el-form-item>
      <el-form-item label="预约练车规则" prop="rule">
        <el-input v-model="dataForm.rule" placeholder="预约练车规则" type="textarea"></el-input>
      </el-form-item>
      <el-form-item label="可提前预约天数" prop="days">
        <el-input v-model="dataForm.days" placeholder="可提前预约天数"></el-input>
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
        startTime: '',
        endTime: '',
        carPrice: '',
        money1: '',
        money2: '',
        point1: '',
        point2: '',
        rule: '',
        days: '',
        examRule: '',
      },
      dataRule: {
        startTime: [
          { required: true, message: '预约开始时间不能为空', trigger: 'blur' }
        ],
        endTime: [
          { required: true, message: '预约结束时间不能为空', trigger: 'blur' }
        ],
        carPrice: [
          { required: true, message: '拼车每公里价格不能为空', trigger: 'blur' }
        ],
        money1: [
          { required: true, message: '一级佣金不能为空', trigger: 'blur' }
        ],
        money2: [
          { required: true, message: '二级佣金不能为空', trigger: 'blur' }
        ],
        point1: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        point2: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ],
        rule: [
          { required: true, message: '预约规则不能为空', trigger: 'blur' }
        ],
        days: [
          { required: true, message: '可提前预约天数不能为空', trigger: 'blur' }
        ],
        examRule: [
          { required: true, message: '预约考试规则不能为空', trigger: 'blur' }
        ],
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
            url: this.$http.adornUrl(`/sys/setting/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.startTime = data.setting.startTime
              this.dataForm.endTime = data.setting.endTime
              this.dataForm.carPrice = data.setting.carPrice
              this.dataForm.money1 = data.setting.money1
              this.dataForm.money2 = data.setting.money2
              this.dataForm.point1 = data.setting.point1
              this.dataForm.point2 = data.setting.point2
              this.dataForm.rule = data.setting.rule
              this.dataForm.days = data.setting.days
              this.dataForm.examRule = data.setting.examRule
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
            url: this.$http.adornUrl(`/sys/setting/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'startTime': this.dataForm.startTime,
              'endTime': this.dataForm.endTime,
              'carPrice': this.dataForm.carPrice,
              'money1': this.dataForm.money1,
              'money2': this.dataForm.money2,
              'point1': this.dataForm.point1,
              'point2': this.dataForm.point2,
              'rule': this.dataForm.rule,
              'days': this.dataForm.days,
              examRule: this.dataForm.examRule,
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
