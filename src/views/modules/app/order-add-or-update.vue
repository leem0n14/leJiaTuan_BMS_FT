<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="orderId">
      <el-input v-model="dataForm.orderId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="商品ID" prop="goodId">
      <el-input v-model="dataForm.goodId" placeholder="商品ID"></el-input>
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
    <el-form-item label="下单数量" prop="orderNum">
      <el-input v-model="dataForm.orderNum" placeholder="下单数量"></el-input>
    </el-form-item>
    <el-form-item label="下单时间" prop="orderTime">
      <el-input v-model="dataForm.orderTime" placeholder="下单时间"></el-input>
    </el-form-item>
    <el-form-item label="订单状态{ 0 未付款 10交易完成 20申请退款 21已退款 30 已取消 40 拼团中 50拼团成功}" prop="orderStatu">
      <el-input v-model="dataForm.orderStatu" placeholder="订单状态{ 0 未付款 10交易完成 20申请退款 21已退款 30 已取消 40 拼团中 50拼团成功}"></el-input>
    </el-form-item>
    <el-form-item label="实际付款金额" prop="realPrice">
      <el-input v-model="dataForm.realPrice" placeholder="实际付款金额"></el-input>
    </el-form-item>
    <el-form-item label="订单类型{0 普通订单  1秒杀订单 2拼团订单}" prop="ordderType">
      <el-input v-model="dataForm.ordderType" placeholder="订单类型{0 普通订单  1秒杀订单 2拼团订单}"></el-input>
    </el-form-item>
    <el-form-item label="订单设定结束时间" prop="endtime">
      <el-input v-model="dataForm.endtime" placeholder="订单设定结束时间"></el-input>
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
          orderId: '',
          goodId: '',
          goodName: '',
          userId: '',
          userName: '',
          groupId: '',
          orderNum: '',
          orderTime: '',
          orderStatu: '',
          realPrice: '',
          ordderType: '',
          endtime: ''
        },
        dataRule: {
          orderId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          goodId: [
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
          orderNum: [
            { required: true, message: '下单数量不能为空', trigger: 'blur' }
          ],
          orderTime: [
            { required: true, message: '下单时间不能为空', trigger: 'blur' }
          ],
          orderStatu: [
            { required: true, message: '订单状态{ 0 未付款 10交易完成 20申请退款 21已退款 30 已取消 40 拼团中 50拼团成功}不能为空', trigger: 'blur' }
          ],
          realPrice: [
            { required: true, message: '实际付款金额不能为空', trigger: 'blur' }
          ],
          ordderType: [
            { required: true, message: '订单类型{0 普通订单  1秒杀订单 2拼团订单}不能为空', trigger: 'blur' }
          ],
          endtime: [
            { required: true, message: '订单设定结束时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/order/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.orderId = data.order.orderId
                this.dataForm.goodId = data.order.goodId
                this.dataForm.goodName = data.order.goodName
                this.dataForm.userId = data.order.userId
                this.dataForm.userName = data.order.userName
                this.dataForm.groupId = data.order.groupId
                this.dataForm.orderNum = data.order.orderNum
                this.dataForm.orderTime = data.order.orderTime
                this.dataForm.orderStatu = data.order.orderStatu
                this.dataForm.realPrice = data.order.realPrice
                this.dataForm.ordderType = data.order.ordderType
                this.dataForm.endtime = data.order.endtime
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
              url: this.$http.adornUrl(`/app/order/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'orderId': this.dataForm.orderId,
                'goodId': this.dataForm.goodId,
                'goodName': this.dataForm.goodName,
                'userId': this.dataForm.userId,
                'userName': this.dataForm.userName,
                'groupId': this.dataForm.groupId,
                'orderNum': this.dataForm.orderNum,
                'orderTime': this.dataForm.orderTime,
                'orderStatu': this.dataForm.orderStatu,
                'realPrice': this.dataForm.realPrice,
                'ordderType': this.dataForm.ordderType,
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
