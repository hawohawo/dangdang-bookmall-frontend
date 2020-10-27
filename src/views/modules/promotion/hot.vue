<template>
  <div class="mod-config">
    <!-- 添加推荐图书对话框 -->
    <el-dialog
      title="图书推荐"
      :visible.sync="dialogVisible"
      width="55%"
      :before-close="handleClose"

    >
      <el-table :data="addHotBook" style="width: 100%">
        <el-table-column prop="id" label="图书id" width="180">
        </el-table-column>
        <el-table-column prop="name" label="图书名称"> </el-table-column>
        <el-table-column prop="priceSj" label="图书售价" width="180">
        </el-table-column>
        <el-table-column
          fixed="right"
          header-align="center"
          align="center"
          width="150"
          label="操作"
        >
          <template slot-scope="scope">
            <el-button
              type="text"
              size="small"
              @click="addHotBookHandle(scope.row.id,scope.row.name)"
              >推荐该图书</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @size-change="sizeChangeAddHotHandle"
        @current-change="currentChangeAddHotHandle"
        :current-page="pageAddHotIndex"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="pageAddHotSize"
        :total="totalAddHotPage"
        layout="total, sizes, prev, pager, next, jumper"
      >
      </el-pagination>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
      </span>
    </el-dialog>

    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList()"
    >
      <el-form-item>
        <el-input
          v-model="dataForm.key"
          placeholder="参数名"
          clearable
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button
          v-if="isAuth('promotion:hot:save')"
          type="primary"
          @click="dialogVisible = true"
          >新增推荐图书</el-button
        >
        <el-button
          v-if="isAuth('promotion:hot:delete')"
          type="danger"
          @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0"
          >批量删除</el-button
        >
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%"
    >
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50"
      >
      </el-table-column>
      <el-table-column
        v-if= false
        prop="hotEntity.id"
        header-align="center"
        align="center"
        label="推荐id"
      >
      </el-table-column>
      <el-table-column
        prop="hotEntity.bookId"
        header-align="center"
        align="center"
        label="图书id"
      >
      </el-table-column>
      <el-table-column
        prop="bookName"
        header-align="center"
        align="center"
        label="图书名称"
      >
      </el-table-column>
      <el-table-column
        prop="hotEntity.status"
        header-align="center"
        align="center"
        label="是否推荐"
      >
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.hotEntity.status"
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
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作"
      >
        <template slot-scope="scope">
          <el-button
            type="text"
            size="small"
            @click="deleteHandle(scope.row.hotEntity.id)"
            >删除</el-button
          >
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
      layout="total, sizes, prev, pager, next, jumper"
    >
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      dataForm: {
        key: "",
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      dialogVisible: false,
      addHotBook: [],
      addDataListLoading: false,

      pageAddHotIndex: 1,
      pageAddHotSize: 10,
      totalAddHotPage: 0,
    };
  },
  components: {},
  activated() {
    this.getDataList(), this.getAddDataList();
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/promotion/hot/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          key: this.dataForm.key,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    // 多选
    selectionChangeHandle(val) {
      this.dataListSelections = val;
    },

    // 删除
    deleteHandle(id) {
      var ids = id
        ? [id]
        : this.dataListSelections.map((item) => {
          console.log(item)
            return item.hotEntity.id;
          });
      this.$confirm(
        `确定对[id=${ids.join(",")}]进行[${id ? "删除" : "批量删除"}]操作?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/promotion/hot/delete"),
          method: "post",
          data: this.$http.adornData(ids, false),
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.getDataList();
              },
            });
          } else {
            this.$message.error(data.msg);
          }
        });
      });
    },
    // 关闭对话框
    handleClose(done) {done();
    },
    // 监听修改上下架状态
    updateStatus(data) {
      console.log("获取状态信息", data);
      // 解构需要更新的字段
      let { id, status } = data.hotEntity;
      this.$http({
        url: this.$http.adornUrl("/promotion/hot/update"),
        method: "post",
        data: this.$http.adornData({ id: id, status: status }, false),
      }).then(({ data }) => {
        this.$message({
          message: "推荐状态成功发生变化",
          type: "success",
          duration: 1500,
          onClose: () => {
            this.getDataList();
          },
        });
      });
    },
    // 推荐图书对话框方法
    // 获取数据列表
    getAddDataList() {
      this.addDataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/promotion/hot/addlist"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageAddHotIndex,
          limit: this.pageAddHotSize,
          key: this.dataForm.key,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.addHotBook = data.page.list;
          this.totalAddHotPage = data.page.totalCount;
        } else {
          this.addHotBook = [];
          this.totalAddHotPage = 0;
        }
        this.addDataListLoading = false;
      });
    },
    // 每页数
    sizeChangeAddHotHandle(val) {
      this.pageAddHotSize = val;
      this.pageAddHotIndex = 1;
      this.getAddDataList();
    },
    // 当前页
    currentChangeAddHotHandle(val) {
      this.pageAddHotIndex = val;
      this.getAddDataList();
    },

    // 推荐图书
    addHotBookHandle(id,bookName) {
      this.$confirm(`确定对[图书：${bookName}]进行["推荐"}]?`, "提示", 
      {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      }
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/promotion/hot/save"),
          method: "post",
          data: this.$http.adornData({
            bookId: id,
            status: "0",
          }),
        }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: '添加推荐成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.dialogVisible = false,
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
           }
           );
      });
    },
  },
};
</script>
