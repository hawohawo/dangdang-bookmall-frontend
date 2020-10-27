<template>
  <div class="mod-config">
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
          v-if="isAuth('promotion:seckill:save')"
          type="primary"
          @click="addOrUpdateHandle()"
          >新增</el-button
        >
        <el-button
          v-if="isAuth('promotion:seckill:delete')"
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
        prop="id"
        header-align="center"
        align="center"
        label="秒杀id"
        width="90"
      >
      </el-table-column>
      <el-table-column
        prop="title"
        header-align="center"
        align="center"
        label="秒杀活动名称"
      >
      </el-table-column>
      <el-table-column
        prop="startDate"
        header-align="center"
        align="center"
        label="秒杀活动开始日期"
        width="140"
      >
      </el-table-column>
      <el-table-column
        prop="endDate"
        header-align="center"
        align="center"
        label="秒杀活动结束日期"
        width="140"
      >
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="是否上线"
        width="90"
      >
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.status"
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
        width="200"
        label="操作"
      >
        <template slot-scope="scope">
          <el-button
            type="text"
            size="small"
            @click="seckillSession(scope.row.id, scope.row.title)"
            >设置秒杀商品</el-button
          >

          <el-button
            type="text"
            size="small"
            @click="addOrUpdateHandle(scope.row.id)"
            >修改</el-button
          >
          <el-button
            type="text"
            size="small"
            @click="deleteHandle(scope.row.id)"
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
    <add-or-update
      v-if="addOrUpdateVisible"
      ref="addOrUpdate"
      @refreshDataList="getDataList"
    ></add-or-update>
    <add-or-update-seckill-book
      v-if="AddOrUpdateSeckillBookVisible"
      ref="AddOrUpdateSeckillBook"
      @refreshDataList="getseckillSessionBook"
    >
    </add-or-update-seckill-book>

    <!-- 第一个抽屉 秒杀时间段+图书数量-->
    <el-drawer :title="title" :visible.sync="table" direction="ltr" size="40%">
      <el-table :data="firstgridData">
        <el-table-column
          v-if="false"
          property="id"
          label="时间段id"
        ></el-table-column>
        <el-table-column
          property="startTime"
          label="开始时间"
        ></el-table-column>
        <el-table-column property="endTime" label="结束时间"></el-table-column>
        <el-table-column property="bookNum" label="商品数量"></el-table-column>
        <el-table-column
          fixed="right"
          header-align="center"
          align="center"
          width="200"
          label="操作"
        >
          <template slot-scope="scope">
            <el-button
              size="small"
              @click="
                seckillSessionBook(
                  scope.row.id,
                  scope.row.startTime,
                  scope.row.endTime
                )
              "
              >查看商品</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </el-drawer>

    <!-- 第二个抽屉，秒杀时间段对应下的商品-->
    <el-drawer
      :title="secKilltitle"
      :visible.sync="secKilltable"
      direction="rtl"
      size="60%"
    >
      <el-button
        v-if="isAuth('promotion:seckill:save')"
        @click="AddOrUpdateSeckillBookHandle()"
        >新增</el-button
      >
      <el-table :data="secondgridData">
        <el-table-column
          property="id"
          label="编号"
          v-if="false"
        ></el-table-column>
        <el-table-column
          property="bookName"
          label="图书名称"
          width="200"
        ></el-table-column>
        <el-table-column property="price" label="图书售价"></el-table-column>
        <el-table-column property="price" label="秒杀价格"></el-table-column>
        <el-table-column property="number" label="秒杀数量"></el-table-column>
        <el-table-column property="sort" label="排序"></el-table-column>
        <el-table-column
          fixed="right"
          header-align="center"
          align="center"
          width="200"
          label="操作"
        >
          <template slot-scope="scope">
            <el-button size="small" @click="AddOrUpdateSeckillBookHandle(scope.row.id)"
              >修改</el-button
            >
            <el-button size="small" @click="seckillSessionBook(scope.row.id)"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </el-drawer>
  </div>
</template>

<script>
import AddOrUpdate from "./seckill-add-or-update";
import AddOrUpdateSeckillBook from "./seckillsessionbook-add-or-update";
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
      addOrUpdateVisible: false,
      table: false,
      secKilltable: false,
      title: "",
      secKilltitle: "",
      firstgridData: [],
      secondgridData: [],
      seckillId: 0,
      seckillSessionId: 0,
      AddOrUpdateSeckillBookVisible: false,
    };
  },
  components: {
    AddOrUpdate,
    AddOrUpdateSeckillBook,
  },
  activated() {
    this.getDataList();
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/promotion/seckill/list"),
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
    // 新增 / 修改 ： 秒杀活动
    addOrUpdateHandle(id) {
      this.addOrUpdateVisible = true;
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id);
      });
    },
    // 新增 / 修改 ：秒杀商品
    AddOrUpdateSeckillBookHandle(id) {
      this.AddOrUpdateSeckillBookVisible = true;
      this.$nextTick(() => {
        this.$refs.AddOrUpdateSeckillBook.init(id);
      });
    },
    // 删除
    deleteHandle(id) {
      var ids = id
        ? [id]
        : this.dataListSelections.map((item) => {
            return item.id;
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
          url: this.$http.adornUrl("/promotion/seckill/delete"),
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
    // 监听修改上下架状态
    updateStatus(data) {
      console.log("获取状态信息", data);
      // 解构需要更新的字段
      let { id, status } = data;
      this.$http({
        url: this.$http.adornUrl("/promotion/seckill/update"),
        method: "post",
        data: this.$http.adornData({ id: id, status: status }, false),
      }).then(({ data }) => {
        this.$message({
          message: "状态成功发生变化",
          type: "success",
          duration: 1500,
          onClose: () => {
            this.getDataList();
          },
        });
      });
    },
    // 查看秒杀活动下时间段
    seckillSession(seckillId, title) {
      console.log("获取秒杀活动的id", seckillId);
      this.seckillId = seckillId;
      // 获取数据 firstgridData
      this.$http({
        url: this.$http.adornUrl(
          `/promotion/seckill/sessionbooknum/${seckillId}`
        ),
        method: "get",
        params: this.$http.adornParams({
          key: this.dataForm.key,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.firstgridData = data.info;
        } else {
          this.firstgridData = [];
        }
      });

      this.title = title;
      this.table = true;
    },
    // 查看秒杀时间段下单商品
    seckillSessionBook(seckillSessionId, startTime, endTime) {
      console.log(
        "秒杀活动id" +
          this.seckillId +
          "获取秒杀活动时间段的id" +
          seckillSessionId
      );
      this.seckillSessionId = seckillSessionId;
      // 获取数据 secondgridData
      this.getseckillSessionBook();

      this.secKilltable = true;
      this.secKilltitle = this.title + "  " + startTime + " - " + endTime;
    },
      getseckillSessionBook(){
      // 获取数据 secondgridData
      this.$http({
        url: this.$http.adornUrl(
          `/promotion/seckill/sessionbookinfo/${this.seckillId}/${this.seckillSessionId}`
        ),
        method: "get",
        params: this.$http.adornParams({
          key: this.dataForm.key,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.secondgridData = data.info;
        } else {
          this.secondgridData = [];
        }
      });
  },



  },
};
</script>
