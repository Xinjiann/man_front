<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="陪玩者ID" prop="playmateId">
      <el-input v-model="dataForm.playmateId" placeholder="陪玩者ID"></el-input>
    </el-form-item>
    <el-form-item label="游戏ID" prop="gameId">
      <el-input v-model="dataForm.gameId" placeholder="游戏ID"></el-input>
    </el-form-item>
    <el-form-item label="价格/h" prop="price">
      <el-input v-model="dataForm.price" placeholder="价格/h"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
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
          playmateId: '',
          gameId: '',
          price: '',
          createTime: '',
          delete: ''
        },
        dataRule: {
          playmateId: [
            { required: true, message: '陪玩者ID不能为空', trigger: 'blur' }
          ],
          gameId: [
            { required: true, message: '游戏ID不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '价格/h不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/companion/companionplaymategamesrelation/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.playmateId = data.companionPlaymateGamesRelation.playmateId
                this.dataForm.gameId = data.companionPlaymateGamesRelation.gameId
                this.dataForm.price = data.companionPlaymateGamesRelation.price
                this.dataForm.createTime = data.companionPlaymateGamesRelation.createTime
                this.dataForm.delete = data.companionPlaymateGamesRelation.delete
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
              url: this.$http.adornUrl(`/companion/companionplaymategamesrelation/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'playmateId': this.dataForm.playmateId,
                'gameId': this.dataForm.gameId,
                'price': this.dataForm.price,
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
