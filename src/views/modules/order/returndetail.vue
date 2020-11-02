<template>
  <div class="mod-config">
    <el-row :gutter="20">
      <el-card class="box-card">
        <div slot="header" class="clearfix">退货订单详情</div>
        <el-col :span="24" v-if="successOrder">
          <span>
            <el-alert
              title="当前退货状态:已完成 该订单目前已关闭"
              type="success"
              show-icon
              style="width: 100%"
              :closable="false"
            >
              <el-link type="primary" href="https://element.eleme.io">
                <div v-html="this.form.code"></div>
              </el-link> </el-alert
          ></span>
        </el-col>
        <el-col :span="24" v-if="doingOrder">
          <span>
            <el-alert
              title="当前退货状态:进行中 该订单目前正在处理中"
              type="warning"
              show-icon
              style="width: 100%"
              :closable="false"
            >
              <el-link type="primary" href="https://element.eleme.io">
                <div v-html="this.form.code"></div>
              </el-link> </el-alert
          ></span>
        </el-col>
        <el-col :span="24" v-if="rejuctOrder">
          <span>
            <el-alert
              title="当前退货状态:已关闭 该订单已被拒绝退货"
              type="error"
              show-icon
              style="width: 100%"
              :closable="false"
            >
              <el-link type="primary" href="https://element.eleme.io">
                <div v-html="this.form.code"></div>
              </el-link> </el-alert
          ></span>
        </el-col>
        <el-row>
          <el-col> </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-button @click.native="selectOrder()">查看订单</el-button>
          </el-col>
        </el-row>
        <el-row>
          <el-row>
            <el-col> </el-col>
          </el-row>
          <i class="el-icon-s-goods">退货商品</i>
        </el-row>
        <el-row>
          <el-card class="box-card">
            <el-row>
              <el-col :span="24">
                <el-table :data="orderInfoData" style="width: 100%" border>
                  <el-table-column
                    prop="bookPic"
                    label="图书图片"
                    align="center"
                  >
                    <template slot-scope="scope">
                      <!-- <el-image
      style="width: 100px; height: 80px"
      :src="scope.row.pic"
      fit="fill"></el-image> -->

                      <img
                        :src="scope.row.bookPic"
                        style="width: 70px; height: 80px"
                      />
                    </template>
                  </el-table-column>
                  <el-table-column
                    prop="bookName"
                    label="图书名称"
                    align="center"
                  >
                  </el-table-column>
                  <el-table-column prop="bookPrice" label="价格" align="center">
                  </el-table-column>
                  <el-table-column prop="bookNum" label="数量" align="center">
                  </el-table-column>
                  <el-table-column prop="priceSum" label="小计" align="center">
                  </el-table-column>
                </el-table>
              </el-col>
            </el-row>
          </el-card>
        </el-row>
        <el-row>
          <i class="el-icon-s-finance">退货单信息</i>
        </el-row>
        <el-card class="box-card">
          <el-row>
            <el-col :span="24">
              <el-form ref="form" :model="form" label-width="160px">
                <el-col :span="12">
                  <el-form-item label="退货单号">
                    <div v-html="form.id"></div>
                  </el-form-item>

                  <el-form-item label="退货状态">
                    <div v-html="form.status"></div>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="订单编号">
                    <el-link type="primary" href="https://element.eleme.io">
                      <div v-html="form.code"></div>
                    </el-link>
                  </el-form-item>
                  <el-form-item label="需退款金额">
                    <div v-html="form.bookPrice"></div>
                  </el-form-item>
                </el-col>
                <el-form-item label="退货地址" v-if="thdzLableActive">
                  <el-select
                    v-model="form.address"
                    placeholder="请选择收货地址"
                    style="width: 80%"
                    @change="displayAddress"
                    v-if="addressSelectActive"
                  >
                    <el-option
                      v-for="item in address"
                      :key="item.id"
                      :label="item.receiveAddr"
                      :value="item.id"
                    >
                    </el-option>
                  </el-select>
                </el-form-item>

                <el-form-item v-if="addressActive" label="收货信息">
                  <div v-html="addressInfoDp"></div>
                </el-form-item>

                <el-form-item>
                  <el-button
                    size="small"
                    type="primary"
                    @click="qrthBtn()"
                    v-if="qrth"
                    >确认退货</el-button
                  >
                  <el-button
                    size="small"
                    type="danger"
                    @click="jjthBtn()"
                    v-if="jjth"
                    >拒绝退货</el-button
                  >
                  <el-button
                    size="small"
                    type="info"
                    @click="qrshBtn()"
                    v-if="qrsh"
                    >确认收货</el-button
                  >
                </el-form-item>
              </el-form>
            </el-col>
          </el-row>
        </el-card>
      </el-card>
    </el-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      addressSelectActive: false,
      dataForm: {
        key: "",
      },
      qrth: false,
      jjth: false,
      qrsh: false,
      orderStatus: 0,
      orderId: 0,
      orderInfoData: [],
      dataListLoading: false,
      active: 0,
      form: {
        id: "",
        status: "",
        orderCode: "",
        address: "",
        price: "",
      },
      address: [],
      addressInfo: "",
      addressInfoDp: "",
      addressActive: false,
      thdzLableActive: false,
      successOrder: false,
      doingOrder: false,
      rejuctOrder: false,
    };
  },
  components: {},
  activated() {
    this.orderId = this.$route.query.returnOrderId;
    //获取
    this.getDataList();
    this.getAddress();
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl(`/order/returninfo/return/${this.orderId}`),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          //获取退货订单目前的状态
          this.orderStatus = data.return[0].status;
          this.addressInfoDp = data.return[0].address;
          this.orderInfoData = data.return;
          this.form = data.return[0];
          if (this.form.status == 1) {
            this.form.status = "待审核";
            this.qrth = true;
            this.jjth = true;
            this.addressSelectActive = true;
            this.thdzLableActive = true;
            this.successOrder = false;
            this.doingOrder = true;
            this.rejuctOrder = false;
          } else if (this.form.status == 4) {
            this.form.status = "拒绝退货";
            this.successOrder = false;
            this.doingOrder = false;
            this.rejuctOrder = true;
          } else if (this.form.status == 3) {
            this.form.status = "已完成";
            this.qrsh = false;
            this.qrth = false;
            this.jjth = false;
            this.successOrder = true;
            this.doingOrder = false;
            this.rejuctOrder = false;
          } else if (this.form.status == 2) {
            this.form.status = "待收货";
            this.qrsh = true;
            this.qrth = false;
            this.jjth = false;
            this.addressActive = true;
            this.addressSelectActive = false;
            this.rejuctOrder = false;
            // this.displayAddress = true;
            this.thdzLableActive = false;
            this.successOrder = false;
            this.doingOrder = true;
          } else {
            this.form.status = "订单异常";
          }

          this.orderInfoData.forEach((item) => {
            item.priceSum = item.bookNum * item.bookPrice;
          });
        } else {
          this.orderInfoData = [];
        }
        this.dataListLoading = false;
      });
    },
    //获取商家收货地址
    getAddress() {
      this.$http({
        url: this.$http.adornUrl("/order/receive/receives"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          //获取退货订单目前的状态
          this.address = data.receives;
          console.log("dizhi" + this.address);
        } else {
          this.address = [];
        }
      });
    },
    //显示地址
    displayAddress(vId) {
      this.$http({
        url: this.$http.adornUrl(`/order/receive/info/${vId}`),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          //获取退货订单目前的状态
          this.addressInfo = data.receive;
          this.addressInfoDp =
            "收货人：" +
            this.addressInfo.receiveName +
            " / 手机号码：" +
            this.addressInfo.receiveTel +
            "  / 邮政编码：" +
            this.addressInfo.postal +
            " / 地址：" +
            this.addressInfo.receiveAddr;
        } else {
          this.addressInfoDp = "读取失败";
        }
      });

      this.addressActive = true;
    },
    jjthBtn() {
      this.$http({
        url: this.$http.adornUrl(`/order/returninfo/updateRefuse`),
        method: "post",
        data: this.$http.adornData({
          id: this.orderId,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "操作成功",
            type: "success",
            duration: 1500,
            onClose: () => {
              this.getDataList();
              this.getAddress();
            },
          });
        } else {
          this.$message.error(data.msg);
        }
      });
    },
    qrthBtn() {
      console.log(this.addressInfoDp);
      this.$http({
        url: this.$http.adornUrl(`/order/returninfo/updateAccept`),
        method: "post",
        data: this.$http.adornData({
          id: this.orderId,
          address: this.addressInfoDp,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "操作成功",
            type: "success",
            duration: 1500,
            onClose: () => {
              this.getDataList();
              this.getAddress();
            },
          });
        } else {
          this.$message.error(data.msg);
        }
      });
    },
    qrshBtn() {
      this.$http({
        url: this.$http.adornUrl(`/order/returninfo/updateReceive`),
        method: "post",
        data: this.$http.adornData({
          id: this.orderId,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: "操作成功",
            type: "success",
            duration: 1500,
            onClose: () => {
              this.getDataList();
              this.getAddress();
            },
          });
        } else {
          this.$message.error(data.msg);
        }
      });
    },
    selectOrder() {
      alert("fdf");
    },
    //修改时间格式
    rTime(date) {
    var json_date = new Date(date).toJSON();
    return new Date(new Date(json_date) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '') 
    },
  },
};
</script>

<style>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
</style>
