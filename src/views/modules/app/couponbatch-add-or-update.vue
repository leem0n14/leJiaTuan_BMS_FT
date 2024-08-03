<template>
  <el-dialog :title="!dataForm.batchId ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
      label-width="120px">
      <el-form-item label="券名称" prop="batchName">
        <el-input v-model="dataForm.batchName" placeholder="券名称"></el-input>
      </el-form-item>
      <el-form-item label="总数量" prop="totalCount">
        <el-input-number v-model="dataForm.totalCount" placeholder="总数量"></el-input-number>
      </el-form-item>
      <el-form-item label="优惠券类型" prop="type">
        <el-radio-group v-model="dataForm.type">
          <el-radio :label="0">全场可用</el-radio>
          <el-radio :label="1">指定驾校</el-radio>
          <el-radio :label="2">指定教练</el-radio>
        </el-radio-group>
        <div v-if="dataForm.type == 1">
          <el-select v-model="dataForm.appointId" placeholder="请选择驾校">
            <el-option v-for="k in schoolList" :key="k.schoolId" :label="k.schoolName" :value="k.schoolId">
            </el-option>
          </el-select>
        </div>
        <div v-else-if="dataForm.type == 2">
          <el-select v-model="schoolId" placeholder="请选择驾校">
            <el-option v-for="k in schoolList" :key="k.schoolId" :label="k.schoolName" :value="k.schoolId">
            </el-option>
          </el-select>
          <el-select v-model="dataForm.appointId" placeholder="请选择教练" @focus="coachFocus()">
            <el-option v-for="k in coachList" :key="k.coachId" :label="k.coachName" :value="k.coachId">
            </el-option>
          </el-select>
        </div>
      </el-form-item>
      <el-form-item label="指定名称" prop="type">
        <el-input v-model="dataForm.appointName" placeholder="指定名称"></el-input>
      </el-form-item>
      <el-form-item label="规则内容" prop="status">
        <el-input v-model="dataForm.ruleContent" placeholder="规则内容"></el-input>
      </el-form-item>
      <el-form-item label="使用门槛" prop="threshold">
        <el-input-number v-model="dataForm.threshold" placeholder="使用门槛"></el-input-number>
      </el-form-item>
      <el-form-item label="优惠金额" prop="amount">
        <el-input-number v-model="dataForm.amount" placeholder="优惠金额"></el-input-number>
      </el-form-item>
      <el-form-item label="每个用户可以领取的数量" prop="receiveCount">
        <el-input-number v-model="dataForm.receiveCount" placeholder="每个用户可以领取的数量"></el-input-number>
      </el-form-item>

      <el-form-item label="领取开始时间" prop="receiveStartedAt">
        <el-date-picker v-model="dataForm.receiveStartedAt" type="datetime" placeholder="选择日期时间"
          value-format="yyyy-MM-dd HH:MM:ss">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="领取结束时间" prop="receiveEndedAt">
        <el-date-picker v-model="dataForm.receiveEndedAt" type="datetime" placeholder="选择日期时间"
          value-format="yyyy-MM-dd HH:MM:ss">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="使用开始时间" prop="useStartedAt">
        <el-date-picker v-model="dataForm.useStartedAt" type="datetime" placeholder="选择日期时间"
          value-format="yyyy-MM-dd HH:MM:ss">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="使用结束时间" prop="useEndedAt">
        <el-date-picker v-model="dataForm.useEndedAt" type="datetime" placeholder="选择日期时间"
          value-format="yyyy-MM-dd HH:MM:ss">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="小程序是否显示" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">否</el-radio>
          <el-radio :label="1">是</el-radio>
        </el-radio-group>
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
        batchId: 0,
        couponName: '',
        totalCount: '',
        status: '',

        batchName: '',//quan名称
        type: null,//优惠券类型，0-全场可用、1-指定驾校、2-指定教练
        appointId: null,//指定使用id，全场为null
        appointName: '',//指定名称
        ruleContent: '',//规则内容
        threshold: '',//使用门槛
        amount: '',//优惠金额
        receiveCount: '',//每个用户可以领取的数量
        receiveStartedAt: '', //领取开始时间
        receiveEndedAt: '',//领取结束时间
        useStartedAt: '',//使用开始时间
        useEndedAt: '',//使用结束时间

      },
      dataRule: {
        batchName: [
          { required: true, message: '券名称不能为空', trigger: 'blur' }
        ],
        totalCount: [
          { required: true, message: '总数量不能为空', trigger: 'blur' }
        ],
        status: [
          { required: true, message: '小程序是否显示不能为空', trigger: 'blur' }
        ],
        type: [
          { required: true, message: '优惠券类型不能为空', trigger: 'blur' }
        ],
        appointName: [
          { required: true, message: '指定名称不能为空', trigger: 'blur' }
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
        ],
      },
      schoolId: null,
      schoolList: [],
      coachList: [],
    }
  },
  watch: {

    'dataForm.type'(newV) {
      this.dataForm.appointId = null
      this.schoolId = null
    },
    schoolId(newV) {
      this.getCoachList(newV)
    }
  },
  mounted() {
    this.getSchoolList()
  },
  methods: {
    coachFocus() {
      if (!this.schoolId)
        this.$message('请先选择驾校')
    },
    //拿教练列表
    getCoachList(schoolId) {
      this.$http({
        url: this.$http.adornUrl('/sys/coach/getlist'),
        method: 'get',
        params: this.$http.adornParams({ schoolId })
      }).then((data) => {
        console.log(data);
        if (data.data.code == 0) {
          this.coachList = data.data.wxCoachEntities
        } else {
          // this.$message.error(data.msg)
        }
      })
    },
    getSchoolList() {
      this.$http({
        url: this.$http.adornUrl('/sys/school/getList'),
        method: 'get',
        params: this.$http.adornParams()
      }).then((data) => {
        // console.log(data);
        this.schoolList = data.data.schoolEntities
      })
    },
    init(id) {
      this.dataForm.batchId = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.batchId) {
          this.$http({
            url: this.$http.adornUrl(`/sys/couponbatch/info/${this.dataForm.batchId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              // this.dataForm.batchName = data.couponBatch.batchName
              this.dataForm.batchName = data.couponBatch.batchName
              // this.dataForm.ruleId = data.couponBatch.ruleId
              this.dataForm.totalCount = data.couponBatch.totalCount
              // this.dataForm.assignCount = data.couponBatch.assignCount
              // this.dataForm.usedCount = data.couponBatch.usedCount
              this.dataForm.status = data.couponBatch.status
              // this.dataForm.name = data.couponBatch.name
              this.dataForm.type = data.couponBatch.type
              this.dataForm.appointId = data.couponBatch.appointId
              this.dataForm.appointName = data.couponBatch.appointName
              this.dataForm.ruleContent = data.couponBatch.ruleContent
              this.dataForm.threshold = data.couponBatch.threshold
              this.dataForm.amount = data.couponBatch.amount
              this.dataForm.receiveCount = data.couponBatch.receiveCount
              this.dataForm.receiveStartedAt = data.couponBatch.receiveStartedAt
              this.dataForm.receiveEndedAt = data.couponBatch.receiveEndedAt
              this.dataForm.useStartedAt = data.couponBatch.useStartedAt
              this.dataForm.useEndedAt = data.couponBatch.useEndedAt

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
            url: this.$http.adornUrl(`/sys/couponbatch/${!this.dataForm.batchId ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'batchId': this.dataForm.batchId || undefined,
              'couponName': this.dataForm.couponName,
              'totalCount': this.dataForm.totalCount,
              'batchName': this.dataForm.batchName,
              'type': this.dataForm.type,
              'appointId': this.dataForm.appointId,
              'appointName': this.dataForm.appointName,
              'ruleContent': this.dataForm.ruleContent,
              'threshold': this.dataForm.threshold,
              'amount': this.dataForm.amount,
              'receiveCount': this.dataForm.receiveCount,
              'receiveStartedAt': this.dataForm.receiveStartedAt,
              'receiveEndedAt': this.dataForm.receiveEndedAt,
              'useStartedAt': this.dataForm.useStartedAt,
              'useEndedAt': this.dataForm.useEndedAt,
              'status': this.dataForm.status,
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
