<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="练车时间" prop="carTime">
        <!-- <el-input v-model="dataForm.carTime" placeholder="练车时间段"></el-input> -->
        <el-time-picker is-range v-model="timeArr" range-separator="至" start-placeholder="开始时间" end-placeholder="结束时间"
          placeholder="选择时间范围" format="HH:mm" value-format="HH:mm">
        </el-time-picker>
      </el-form-item>
      <el-form-item label="时间段限制人数" prop="peopleNumber">
        <el-input v-model="dataForm.peopleNumber" placeholder="时间段限制人数" style="width: 200px;"></el-input>
      </el-form-item>
      <el-form-item label="显示顺序" prop="numbers">
        <el-input v-model="dataForm.numbers" placeholder="输入数字表示顺序" style="width: 200px;"></el-input>
      </el-form-item>
      <el-form-item label="是否接送">
        <el-radio-group v-model="dataForm.receive">
          <el-radio :label="1">可接送</el-radio>
          <el-radio :label="0">不接送</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="是否启用" prop="statu">
        <el-radio v-model="dataForm.statu" :label="0">不启用</el-radio>
        <el-radio v-model="dataForm.statu" :label="1">启用</el-radio>
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
      timeArr: '',
      visible: false,
      dataForm: {
        id: 0,
        carTime: '',
        statu: '',
        numbers: '',
        peopleNumber: '',
        receive: "",
      },
      dataRule: {
        carTime: [
          { required: true, message: '练车时间段不能为空', trigger: 'blur' }
        ],
        statu: [
          { required: true, message: '请选择是否启用', trigger: 'blur' }
        ],
        numbers: [
          { required: true, message: '小程序时间显示顺序不能为空', trigger: 'blur' }
        ],
        peopleNumber: [
          { required: true, message: '练车人数限制不能为空', trigger: 'blur' }
        ],
        receive: [
          { required: true, message: '是否接送不能为空', trigger: 'blur' }
        ]

      }
    }
  },
  watch: {
    timeArr(newV) {
      this.dataForm.carTime = `${newV[0]}-${newV[1]}`
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
            url: this.$http.adornUrl(`/sys/cartime/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              // console.log('111', data);
              this.dataForm.carTime = data.carTime.carTime
              this.timeArr = data.carTime.carTime.split('-')
              this.dataForm.statu = data.carTime.statu
              this.dataForm.peopleNumber = data.carTime.peopleNumber
              this.dataForm.numbers = data.carTime.numbers
              this.dataForm.receive = data.carTime.receive
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
            url: this.$http.adornUrl(`/sys/cartime/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'carTime': this.dataForm.carTime,
              'statu': this.dataForm.statu,
              'numbers': this.dataForm.numbers,
              'peopleNumber': this.dataForm.peopleNumber,
              'receive': this.dataForm.receive,
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
