<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="openid" prop="user">
      <el-input v-model="dataForm.user" placeholder="openid"></el-input>
    </el-form-item>
    <el-form-item label="playmate id" prop="playmate">
      <el-input v-model="dataForm.playmate" placeholder="playmate id"></el-input>
    </el-form-item>
    <el-form-item label="game id" prop="game">
      <el-input v-model="dataForm.game" placeholder="game id"></el-input>
    </el-form-item>
    <el-form-item label="订单状态" prop="orderStatus">
      <el-input v-model="dataForm.orderStatus" placeholder="订单状态"></el-input>
    </el-form-item>
    <el-form-item label="总时间" prop="period">
      <el-input v-model="dataForm.period" placeholder="总时间"></el-input>
    </el-form-item>
    <el-form-item label="总价格" prop="amount">
      <el-input v-model="dataForm.amount" placeholder="总价格"></el-input>
    </el-form-item>
    <el-form-item label="下单时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="下单时间"></el-input>
    </el-form-item>
    <el-form-item label="删除标志" prop="delete">
      <el-input v-model="dataForm.delete" placeholder="删除标志"></el-input>
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
          user: '',
          playmate: '',
          game: '',
          orderStatus: '',
          period: '',
          amount: '',
          createTime: '',
          delete: ''
        },
        dataRule: {
          user: [
            { required: true, message: 'openid不能为空', trigger: 'blur' }
          ],
          playmate: [
            { required: true, message: 'playmate id不能为空', trigger: 'blur' }
          ],
          game: [
            { required: true, message: 'game id不能为空', trigger: 'blur' }
          ],
          orderStatus: [
            { required: true, message: '订单状态不能为空', trigger: 'blur' }
          ],
          period: [
            { required: true, message: '总时间不能为空', trigger: 'blur' }
          ],
          amount: [
            { required: true, message: '总价格不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '下单时间不能为空', trigger: 'blur' }
          ],
          delete: [
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
              url: this.$http.adornUrl(`/companion/companionorder/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.user = data.companionOrder.user
                this.dataForm.playmate = data.companionOrder.playmate
                this.dataForm.game = data.companionOrder.game
                this.dataForm.orderStatus = data.companionOrder.orderStatus
                this.dataForm.period = data.companionOrder.period
                this.dataForm.amount = data.companionOrder.amount
                this.dataForm.createTime = data.companionOrder.createTime
                this.dataForm.delete = data.companionOrder.delete
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
              url: this.$http.adornUrl(`/companion/companionorder/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'user': this.dataForm.user,
                'playmate': this.dataForm.playmate,
                'game': this.dataForm.game,
                'orderStatus': this.dataForm.orderStatus,
                'period': this.dataForm.period,
                'amount': this.dataForm.amount,
                'createTime': this.dataForm.createTime,
                'delete': this.dataForm.delete
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
