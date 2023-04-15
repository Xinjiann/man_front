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
    <el-form-item label="分数" prop="rating">
      <el-input v-model="dataForm.rating" placeholder="分数"></el-input>
    </el-form-item>
    <el-form-item label="平均" prop="comment">
      <el-input v-model="dataForm.comment" placeholder="平均"></el-input>
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
          rating: '',
          comment: '',
          delete: ''
        },
        dataRule: {
          user: [
            { required: true, message: 'openid不能为空', trigger: 'blur' }
          ],
          playmate: [
            { required: true, message: 'playmate id不能为空', trigger: 'blur' }
          ],
          rating: [
            { required: true, message: '分数不能为空', trigger: 'blur' }
          ],
          comment: [
            { required: true, message: '平均不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/companion/companionreview/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.user = data.companionReview.user
                this.dataForm.playmate = data.companionReview.playmate
                this.dataForm.rating = data.companionReview.rating
                this.dataForm.comment = data.companionReview.comment
                this.dataForm.delete = data.companionReview.delete
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
              url: this.$http.adornUrl(`/companion/companionreview/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'user': this.dataForm.user,
                'playmate': this.dataForm.playmate,
                'rating': this.dataForm.rating,
                'comment': this.dataForm.comment,
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
