<template>
  <div class="mod-config">
    <el-row :gutter="20">
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <el-col :span="5">
            <span>
              <el-alert
                title="当前订单状态:待发货"
                type="warning"
                show-icon
                style="width: 100%"
                :closable="false"
              >
              </el-alert
            ></span>
          </el-col>
        </div>

        <el-row>
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
                    <el-link
                      type="primary"
                      href="https://element.eleme.io"
                    >
                    
                    <div v-html="form.code"></div>

                    </el-link>
                  </el-form-item>
                  <el-form-item label="需退款金额">
                    <div v-html="form.bookPrice"></div>
                  </el-form-item>
                </el-col>
                <el-form-item label="退货地址">
                  <el-select
                    v-model="form.address"
                    placeholder="请选择收货地址"
                    style="width: 80%"
                    @change="displayAddress"
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
                  <!-- <el-button size="small" type="primary" @click="onSubmit"
                    >确认退货</el-button
                  > -->
                  <el-button size="small" type="danger">拒绝退货</el-button>
                  <el-button size="small" type="info">确认收货</el-button>
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
      dataForm: {
        key: "",
      },
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
          this.orderStatus = data.return.status;

          this.orderInfoData = data.return;
          this.form = data.return[0];
          if (this.form.status == 1) {
            this.form.status = "待审核";
          } else if (this.form.status == 2) {
            this.form.status = "拒绝退货";
          } else if (this.form.status == 3) {
            this.form.status = "已完成";
          } else if (this.form.status == 4) {
            this.form.status = "待审核";
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
          console.log("daddsada" + this.addressInfo);
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
