<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
    <el-form-item label="秒杀活动名称" prop="title">
      <el-input v-model="dataForm.title" placeholder=""></el-input>
    </el-form-item>
    <!-- <el-form-item label="秒杀开始时间" prop="startDate">
      <el-input v-model="dataForm.startDate" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="秒杀结束时间" prop="endDate">
      <el-input v-model="dataForm.endDate" placeholder=""></el-input>
    </el-form-item> -->
      <el-form-item label="活动时间">
    <el-col :span="11">
      <el-date-picker type="date" placeholder="活动开始日期" v-model="dataForm.startDate" style="width: 80%;"></el-date-picker>
    </el-col>
    <el-col class="line" :span="2">-</el-col>
    <el-col :span="11">
      <el-date-picker type="date" placeholder="活动结束日期" v-model="dataForm.endDate" style="width: 80%;"></el-date-picker>
    </el-col>
  </el-form-item>

    <el-form-item label="是否立即上线" prop="status">
     <el-switch
            v-model="dataForm.status"
            active-color="#13ce66"
            inactive-color="#ff4949"
            :active-value="1"
            :inactive-value="0"
          >
                </el-switch>
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
          title: '',
          startDate: '',
          endDate: '',
          status: ''
        },
        dataRule: {
          title: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          startDate: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          endDate: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '0 未上线 1 上线不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/promotion/seckill/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.seckill.title
                this.dataForm.startDate = data.seckill.startDate
                this.dataForm.endDate = data.seckill.endDate
                this.dataForm.status = data.seckill.status
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
              url: this.$http.adornUrl(`/promotion/seckill/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'startDate': this.dataForm.startDate,
                'endDate': this.dataForm.endDate,
                'status': this.dataForm.status
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
