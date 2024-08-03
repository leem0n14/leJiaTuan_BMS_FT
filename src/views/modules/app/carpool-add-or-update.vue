<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="微信分账单号" prop="orderId">
        <el-input v-model="dataForm.orderId" placeholder="微信分账单号"></el-input>
      </el-form-item>
      <el-form-item label="微信支付订单号" prop="outOrderNo">
        <el-input v-model="dataForm.outOrderNo" placeholder="微信支付订单号"></el-input>
      </el-form-item>
      <el-form-item label="起点" prop="startPlace">
        <el-input v-model="dataForm.startPlace" placeholder="起点"></el-input>
      </el-form-item>
      <el-form-item label="终点" prop="endPlace">
        <el-input v-model="dataForm.endPlace" placeholder="终点"></el-input>
      </el-form-item>
      <el-form-item label="发起用户" prop="fqUserid">
        <el-input v-model="dataForm.fqUserid" placeholder="发起用户"></el-input>
      </el-form-item>
      <el-form-item label="发起人姓名" prop="fqName">
        <el-input v-model="dataForm.fqName" placeholder="发起人姓名"></el-input>
      </el-form-item>
      <el-form-item label="发起人电话" prop="fqPhone">
        <el-input v-model="dataForm.fqPhone" placeholder="发起人电话"></el-input>
      </el-form-item>
      <el-form-item label="接单用户" prop="jdUserid">
        <el-input v-model="dataForm.jdUserid" placeholder="接单用户"></el-input>
      </el-form-item>
      <el-form-item label="接单姓名" prop="jdName">
        <el-input v-model="dataForm.jdName" placeholder="接单姓名"></el-input>
      </el-form-item>
      <el-form-item label="接单电话" prop="jdPhone">
        <el-input v-model="dataForm.jdPhone" placeholder="接单电话"></el-input>
      </el-form-item>
      <el-form-item label="发起时间" prop="startTime">
        <el-input v-model="dataForm.startTime" placeholder="发起时间"></el-input>
      </el-form-item>
      <el-form-item label="结束时间" prop="endTime">
        <el-input v-model="dataForm.endTime" placeholder="结束时间"></el-input>
      </el-form-item>
      <el-form-item label="是否拼车成功" prop="statu">
        <el-input v-model="dataForm.statu" placeholder="是否拼车成功"></el-input>
        <el-radio-group v-model="dataForm.statu">
          <el-radio :label="0">拼车中</el-radio>
          <el-radio :label="1">拼车成功</el-radio>
          <el-radio :label="2">拼车失败</el-radio>
          <el-radio :label="3">未付款</el-radio>
          <el-radio :label="4">付款失败</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="价格" prop="price">
        <el-input v-model="dataForm.price" placeholder="价格"></el-input>
      </el-form-item>
      <el-form-item label="车牌号" prop="carId">
        <el-input v-model="dataForm.carId" placeholder="车牌号"></el-input>
      </el-form-item>
      <el-form-item label="车型" prop="carType">
        <el-input v-model="dataForm.carType" placeholder="车型"></el-input>
      </el-form-item>
      <el-form-item label="出发时间" prop="time">
        <el-input v-model="dataForm.time" placeholder="出发时间"></el-input>
      </el-form-item>
      <el-form-item label="分账结果" prop="result">
        <el-input v-model="dataForm.result" placeholder="分账结果"></el-input>
      </el-form-item>
      <el-form-item label="分账失败原因" prop="failReason">
        <el-input v-model="dataForm.failReason" placeholder="分账失败原因"></el-input>
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
        orderId: '',
        outOrderNo: '',
        startPlace: '',
        endPlace: '',
        fqUserid: '',
        fqName: '',
        fqPhone: '',
        jdUserid: '',
        jdName: '',
        jdPhone: '',
        startTime: '',
        endTime: '',
        statu: '',
        price: '',
        carId: '',
        carType: '',
        time: '',
        result: '',
        failReason: ''
      },
      dataRule: {
        orderId: [
          { required: true, message: '微信分账单号不能为空', trigger: 'blur' }
        ],
        outOrderNo: [
          { required: true, message: '微信支付订单号不能为空', trigger: 'blur' }
        ],
        startPlace: [
          { required: true, message: '起点不能为空', trigger: 'blur' }
        ],
        endPlace: [
          { required: true, message: '终点不能为空', trigger: 'blur' }
        ],
        fqUserid: [
          { required: true, message: '发起用户不能为空', trigger: 'blur' }
        ],
        fqName: [
          { required: true, message: '发起人姓名不能为空', trigger: 'blur' }
        ],
        fqPhone: [
          { required: true, message: '发起人电话不能为空', trigger: 'blur' }
        ],
        jdUserid: [
          { required: true, message: '接单用户不能为空', trigger: 'blur' }
        ],
        jdName: [
          { required: true, message: '接单姓名不能为空', trigger: 'blur' }
        ],
        jdPhone: [
          { required: true, message: '接单电话不能为空', trigger: 'blur' }
        ],
        startTime: [
          { required: true, message: '发起时间不能为空', trigger: 'blur' }
        ],
        endTime: [
          { required: true, message: '结束时间不能为空', trigger: 'blur' }
        ],
        statu: [
          { required: true, message: '是否拼车成功{0 拼车中 1拼车成功 2拼车失败 3未付款 4付款失败}不能为空', trigger: 'blur' }
        ],
        price: [
          { required: true, message: '价格不能为空', trigger: 'blur' }
        ],
        carId: [
          { required: true, message: '车牌号不能为空', trigger: 'blur' }
        ],
        carType: [
          { required: true, message: '车型不能为空', trigger: 'blur' }
        ],
        time: [
          { required: true, message: '出发时间不能为空', trigger: 'blur' }
        ],
        result: [
          { required: true, message: '分账结果不能为空', trigger: 'blur' }
        ],
        failReason: [
          { required: true, message: '分账失败原因不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/app/carpool/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.orderId = data.carpool.orderId
              this.dataForm.outOrderNo = data.carpool.outOrderNo
              this.dataForm.startPlace = data.carpool.startPlace
              this.dataForm.endPlace = data.carpool.endPlace
              this.dataForm.fqUserid = data.carpool.fqUserid
              this.dataForm.fqName = data.carpool.fqName
              this.dataForm.fqPhone = data.carpool.fqPhone
              this.dataForm.jdUserid = data.carpool.jdUserid
              this.dataForm.jdName = data.carpool.jdName
              this.dataForm.jdPhone = data.carpool.jdPhone
              this.dataForm.startTime = data.carpool.startTime
              this.dataForm.endTime = data.carpool.endTime
              this.dataForm.statu = data.carpool.statu
              this.dataForm.price = data.carpool.price
              this.dataForm.carId = data.carpool.carId
              this.dataForm.carType = data.carpool.carType
              this.dataForm.time = data.carpool.time
              this.dataForm.result = data.carpool.result
              this.dataForm.failReason = data.carpool.failReason
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
            url: this.$http.adornUrl(`/app/carpool/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'orderId': this.dataForm.orderId,
              'outOrderNo': this.dataForm.outOrderNo,
              'startPlace': this.dataForm.startPlace,
              'endPlace': this.dataForm.endPlace,
              'fqUserid': this.dataForm.fqUserid,
              'fqName': this.dataForm.fqName,
              'fqPhone': this.dataForm.fqPhone,
              'jdUserid': this.dataForm.jdUserid,
              'jdName': this.dataForm.jdName,
              'jdPhone': this.dataForm.jdPhone,
              'startTime': this.dataForm.startTime,
              'endTime': this.dataForm.endTime,
              'statu': this.dataForm.statu,
              'price': this.dataForm.price,
              'carId': this.dataForm.carId,
              'carType': this.dataForm.carType,
              'time': this.dataForm.time,
              'result': this.dataForm.result,
              'failReason': this.dataForm.failReason
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
