<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="金额" prop="price">
      <el-input v-model="dataForm.price" placeholder="金额"></el-input>
    </el-form-item>
    <el-form-item label="国际单号" prop="orderNumber">
      <el-input v-model="dataForm.orderNumber" placeholder="国际单号"></el-input>
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
          remark: '',
          trackingNumber: '',
          address: '',
          openid: '',
          createTime: '',
          price: '',
          orderNumber: '',
          image: '',
          status: '',
          deleted: ''
        },
        dataRule: {
          remark: [
            { required: true, message: '描述不能为空', trigger: 'blur' }
          ],
          trackingNumber: [
            { required: true, message: '快递单号不能为空', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '地址不能为空', trigger: 'blur' }
          ],
          openid: [
            { required: true, message: '创建者不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '金额不能为空', trigger: 'blur' }
          ],
          orderNumber: [
            { required: true, message: '国际单号不能为空', trigger: 'blur' }
          ],
          image: [
            { required: true, message: '图片key不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '订单状态不能为空', trigger: 'blur' }
          ],
          deleted: [
            { required: true, message: '删除标志不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/shipping/order/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.remark = data.order.remark
                this.dataForm.trackingNumber = data.order.trackingNumber
                this.dataForm.address = data.order.address
                this.dataForm.openid = data.order.openid
                this.dataForm.createTime = data.order.createTime
                this.dataForm.price = data.order.price
                this.dataForm.orderNumber = data.order.orderNumber
                this.dataForm.image = data.order.image
                this.dataForm.status = data.order.status
                this.dataForm.deleted = data.order.deleted
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
              url: this.$http.adornUrl(`/shipping/order/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'remark': this.dataForm.remark,
                'trackingNumber': this.dataForm.trackingNumber,
                'address': this.dataForm.address,
                'openid': this.dataForm.openid,
                'createTime': this.dataForm.createTime,
                'price': this.dataForm.price,
                'orderNumber': this.dataForm.orderNumber,
                'image': this.dataForm.image,
                'status': this.dataForm.status,
                'deleted': this.dataForm.deleted
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
