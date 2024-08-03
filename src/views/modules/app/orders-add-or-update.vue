<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="微信预付订单号" prop="orderId">
        <el-input v-model="dataForm.orderId" placeholder="微信预付订单号"></el-input>
      </el-form-item>
      <el-form-item label="退款订单号" prop="refundId">
        <el-input v-model="dataForm.refundId" placeholder="退款订单号"></el-input>
      </el-form-item>
      <el-form-item label="商品ID" prop="goodsId">
        <el-input v-model="dataForm.goodsId" placeholder="商品ID"></el-input>
      </el-form-item>
      <el-form-item label="商品名" prop="goodName">
        <el-input v-model="dataForm.goodName" placeholder="商品名"></el-input>
      </el-form-item>
      <el-form-item label="用户ID" prop="userId">
        <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
      </el-form-item>
      <el-form-item label="用户名" prop="userName">
        <el-input v-model="dataForm.userName" placeholder="用户名"></el-input>
      </el-form-item>
      <el-form-item label="拼团ID" prop="groupId">
        <el-input v-model="dataForm.groupId" placeholder="拼团ID"></el-input>
      </el-form-item>
      <el-form-item label="下单数量" prop="number">
        <el-input v-model="dataForm.number" placeholder="下单数量"></el-input>
      </el-form-item>
      <el-form-item label="创建时间" prop="createTime">
        <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
      </el-form-item>
      <el-form-item label="订单完成时间" prop="orderTime">
        <el-input v-model="dataForm.orderTime" placeholder="订单完成时间"></el-input>
      </el-form-item>
      <el-form-item label="订单状态{ 0 未付款 1交易完成 2申请退款 3已退款  4 拼团中 5拼团成功 6退款失败 }" prop="statu">
        <el-input v-model="dataForm.statu" placeholder="订单状态{ 0 未付款 1交易完成 2申请退款 3已退款  4 拼团中 5拼团成功 6退款失败 }"></el-input>
      </el-form-item>
      <el-form-item label="实际付款金额" prop="realPrice">
        <el-input v-model="dataForm.realPrice" placeholder="实际付款金额"></el-input>
      </el-form-item>
      <el-form-item label="订单类型{0 普通订单  1秒杀订单 2拼团订单}" prop="type">
        <el-input v-model="dataForm.type" placeholder="订单类型{0 普通订单  1秒杀订单 2拼团订单}"></el-input>
      </el-form-item>
      <el-form-item label="订单设定结束时间" prop="endTime">
        <el-input v-model="dataForm.endTime" placeholder="订单设定结束时间"></el-input>
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
        refundId: '',
        goodsId: '',
        goodName: '',
        userId: '',
        userName: '',
        groupId: '',
        number: '',
        createTime: '',
        orderTime: '',
        statu: '',
        realPrice: '',
        type: '',
        endTime: ''
      },
      dataRule: {
        orderId: [
          { required: true, message: '微信预付订单号不能为空', trigger: 'blur' }
        ],
        refundId: [
          { required: true, message: '退款订单号不能为空', trigger: 'blur' }
        ],
        goodsId: [
          { required: true, message: '商品ID不能为空', trigger: 'blur' }
        ],
        goodName: [
          { required: true, message: '商品名不能为空', trigger: 'blur' }
        ],
        userId: [
          { required: true, message: '用户ID不能为空', trigger: 'blur' }
        ],
        userName: [
          { required: true, message: '用户名不能为空', trigger: 'blur' }
        ],
        groupId: [
          { required: true, message: '拼团ID不能为空', trigger: 'blur' }
        ],
        number: [
          { required: true, message: '下单数量不能为空', trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: '创建时间不能为空', trigger: 'blur' }
        ],
        orderTime: [
          { required: true, message: '订单完成时间不能为空', trigger: 'blur' }
        ],
        statu: [
          { required: true, message: '订单状态{ 0 未付款 1交易完成 2申请退款 3已退款  4 拼团中 5拼团成功 6退款失败 }不能为空', trigger: 'blur' }
        ],
        realPrice: [
          { required: true, message: '实际付款金额不能为空', trigger: 'blur' }
        ],
        type: [
          { required: true, message: '订单类型{0 普通订单  1秒杀订单 2拼团订单}不能为空', trigger: 'blur' }
        ],
        endTime: [
          { required: true, message: '订单设定结束时间不能为空', trigger: 'blur' }
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
            url: this.$http.adornUrl(`/sys/orders/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.orderId = data.orders.orderId
              this.dataForm.refundId = data.orders.refundId
              this.dataForm.goodsId = data.orders.goodsId
              this.dataForm.goodName = data.orders.goodName
              this.dataForm.userId = data.orders.userId
              this.dataForm.userName = data.orders.userName
              this.dataForm.groupId = data.orders.groupId
              this.dataForm.number = data.orders.number
              this.dataForm.createTime = data.orders.createTime
              this.dataForm.orderTime = data.orders.orderTime
              this.dataForm.statu = data.orders.statu
              this.dataForm.realPrice = data.orders.realPrice
              this.dataForm.type = data.orders.type
              this.dataForm.endTime = data.orders.endTime
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
            url: this.$http.adornUrl(`/sys/orders/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'orderId': this.dataForm.orderId,
              'refundId': this.dataForm.refundId,
              'goodsId': this.dataForm.goodsId,
              'goodName': this.dataForm.goodName,
              'userId': this.dataForm.userId,
              'userName': this.dataForm.userName,
              'groupId': this.dataForm.groupId,
              'number': this.dataForm.number,
              'createTime': this.dataForm.createTime,
              'orderTime': this.dataForm.orderTime,
              'statu': this.dataForm.statu,
              'realPrice': this.dataForm.realPrice,
              'type': this.dataForm.type,
              'endTime': this.dataForm.endTime
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
