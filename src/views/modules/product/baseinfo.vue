<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('product:baseinfo:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('product:baseinfo:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.id"
        header-align="center"
        align="center"
        label="自增主键"
        v-if="false">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.picture"
        header-align="center"
        align="center"
        label="图书图片">

<template slot-scope="scope">

                  <img
                    :src="scope.row.baseinfoEntity.picture"
                    style="width: 70px; height: 80px"
                  />
                </template>


      </el-table-column>
      <el-table-column
        prop="name1"
        header-align="center"
        align="center"
        label="图书分类">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.name"
        header-align="center"
        align="center"
        label="图书名称">
      </el-table-column>

      <el-table-column
        prop="baseinfoEntity.author"
        header-align="center"
        align="center"
        label="作者">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.score"
        header-align="center"
        align="center"
        label="图书评分">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.priceDj"
        header-align="center"
        align="center"
        label="图书定价">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.priceSj"
        header-align="center"
        align="center"
        label="图书售价">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.priceYf"
        header-align="center"
        align="center"
        label="运费">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.insale"
        header-align="center"
        align="center"
        label="是否上架">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.baseinfoEntity.insale"
            active-color="#13ce66"
            inactive-color="#ff4949"
            :active-value="1"
            :inactive-value="0"
            @change="updateStatus(scope.row)"
          >
          </el-switch>
        </template>
      </el-table-column>

      <el-table-column
        prop="baseinfoEntity.stock"
        header-align="center"
        align="center"
        label="图书库存">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.integral"
        header-align="center"
        align="center"
        label="积分">
      </el-table-column>
      
      <el-table-column
        prop="baseinfoEntity.publishId"
        header-align="center"
        align="center"
        label="出版信息ID"
        v-if="false">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.bookdetailId"
        header-align="center"
        align="center"
        label="图书详情ID"
        v-if="false">
      </el-table-column>
      <el-table-column
        prop="baseinfoEntity.commentId"
        header-align="center"
        align="center"
        label="评论ID"
        v-if="false">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.baseinfoEntity.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.baseinfoEntity.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './baseinfo-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/product/baseinfo/books'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/product/baseinfo/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },
          // 监听修改上下架状态
    updateStatus (data) {
      // 解构需要更新的字段
      let {id, insale} = data.baseinfoEntity
      this.$http({
        url: this.$http.adornUrl('/product/baseinfo/update'),
        method: 'post',
        data: this.$http.adornData({id: id, insale: insale}, false)
      }).then(({data}) => {
        this.$message({
          message: ('状态成功发生变化'),
          type: 'success',
          duration: 1500,
          onClose: () => {
            this.getDataList()
          }
        })
      })
    }
    }
  }
</script>
