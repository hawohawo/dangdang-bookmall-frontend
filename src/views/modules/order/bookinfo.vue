<template>
  <div class="mod-config">
    <el-row>
      <el-col :span="24">
        <el-steps
          v-if="stepActive"
          :active="orderStatus"
          align-center
          finish-status="success"
        >
          <el-step title="提交订单"></el-step>
          <el-step title="支付订单"></el-step>
          <el-step title="平台发货"></el-step>
          <el-step title="确认收货"></el-step>
          <el-step title="完成评价"></el-step>
        </el-steps>
      </el-col>
    </el-row>
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
          <el-col :span="19">
            <el-button
              size="mini"
              v-if="xgButtonActice"
              style="float: right; margin: 6px"
              >修改收货人信息</el-button
            >
            <el-button
              size="mini"
              type="danger"
              v-if="qxButtonActice"
              style="float: right; margin: 6px"
              >取消订单</el-button
            >
            <el-button
              size="mini"
              v-if="zzButtonActice"
              style="float: right; margin: 6px"
              >订单跟踪</el-button
            >
            <el-button
              size="mini"
              type="info"
              v-if="fhButtonActice"
              style="float: right; margin: 6px"
              @click="orderFH()"
              >订单发货</el-button
            >
          </el-col>
        </div>
        <el-row>
          <i class="el-icon-s-finance">订单基本信息</i>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-table :data="orderInfoData" style="width: 100%" border>
              <el-table-column prop="code" label="订单编号" align="center">
              </el-table-column>
              <el-table-column prop="userId" label="用户id" align="center">
              </el-table-column>

              <el-table-column
                prop="logisticsNum"
                label="发货单流水号"
                align="center"
              >
              </el-table-column>
              <el-table-column prop="freight" label="运费" align="center">
              </el-table-column>
              <el-table-column
                prop="paymentType"
                label="支付方式"
                align="center"
              >
              </el-table-column>
              <el-table-column
                prop="paymentTime"
                label="付款时间"
                align="center"
              >
              </el-table-column>
            </el-table>

            <el-table
              :data="baseOrderInfoData"
              style="width: 100%; height: 130px"
              border
            >
              <el-table-column prop="timeXd" label="下单时间" align="center">
              </el-table-column>

              <el-table-column
                prop="deliverTime"
                label="发货时间"
                align="center"
              >
              </el-table-column>
              <el-table-column
                prop="reciveTime"
                label="收货时间"
                align="center"
              >
              </el-table-column>
              <el-table-column
                prop="autorecive"
                label="订单自动收货时间"
                align="center"
              >
              </el-table-column>
              <el-table-column prop="totalJF" label="可获积分" align="center">
              </el-table-column>
              <el-table-column prop="totalJF" label="可获成长值" align="center">
              </el-table-column>
            </el-table>
          </el-col>
        </el-row>

        <el-row>
          <i class="el-icon-s-promotion">收货人信息</i>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-table :data="orderInfoData" style="width: 100%" border>
              <el-table-column prop="userName" label="收货人" align="center">
              </el-table-column>
              <el-table-column prop="userPhone" label="手机号码" align="center">
              </el-table-column>

              <el-table-column prop="postal" label="邮政编码" align="center">
              </el-table-column>
              <el-table-column prop="address" label="收货地址" align="center">
              </el-table-column>
            </el-table>
          </el-col>
        </el-row>

        <el-row>
          <i class="el-icon-s-goods">商品详情</i>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-table :data="bookData" style="width: 100%" border>
              <el-table-column prop="bookPic" label="图书图片" align="center">
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
              <el-table-column prop="bookName" label="图书名称" align="center">
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

        <el-row>
          <i class="el-icon-coin">费用详情</i>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-table :data="priceData" style="width: 100%" border>
              <el-table-column prop="pricezj" label="商品合计" align="center">
              </el-table-column>
              <el-table-column prop="freight" label="运费" align="center">
              </el-table-column>
              <el-table-column
                prop="discountCoupon"
                label="优惠券折扣"
                align="center"
              >
              </el-table-column>
              <el-table-column
                prop="discountScore"
                label="积分折扣"
                align="center"
              >
              </el-table-column>
              <el-table-column prop="priceYf" label="应付款金额" align="center">
              </el-table-column>
            </el-table>
          </el-col>
        </el-row>

        <el-row>
          <i class="el-icon-setting">操作记录</i>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-table :data="recordData" style="width: 100%" border>
              <el-table-column prop="user" label="操作者" align="center">
              </el-table-column>
              <el-table-column prop="time" label="操作时间" align="center">
              </el-table-column>
              <el-table-column prop="status" label="订单状态" align="center">
              </el-table-column>
              <el-table-column prop="remark" label="备注" align="center">
              </el-table-column>
            </el-table>
          </el-col>
        </el-row>
      </el-card>
    </el-row>

      <el-dialog
    title="订单发货"
    :close-on-click-modal="false"
    :visible.sync="fhVisible">
    <el-form   label-width="120px">

    <el-form-item label="物流单号" prop="url">
      <el-input v-model="wldh" placeholder=""></el-input>
    </el-form-item>
 
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="qrfh(wldh)">确定</el-button>
    </span>
  </el-dialog>
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
      baseOrderInfoData: [],
      priceData: [],
      recordData: [],
      bookData: [],
      dataListLoading: false,
      active: 0,
      stepActive: false,
      // 不同状态对应不同按钮
      xgButtonActice: false,
      zzButtonActice: false,
      fhButtonActice: false,
      qxButtonActice: false,

      fhVisible:false,
      wldh:""
    };
  },
  components: {},
  activated() {
    this.orderId = this.$route.query.orderId;
    //获取
    this.getDataList();
    this.getRecordList();
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl(`/order/orderinfo/detail/${this.orderId}`),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          //获取订单目前的状态
          this.orderStatus = data.order[0].status;
          //解析订单所属步骤
          if (this.orderStatus == "0") {
            this.orderStatus = 1;
            this.qxButtonActice = true;
            this.fhButtonActice = false;
            this.xgButtonActice = false;
            this.zzButtonActice = false;
          } else if (this.orderStatus == "1") {
            this.orderStatus = 2;
            this.fhButtonActice = true;
            this.xgButtonActice = true;
            this.zzButtonActice = false;
            this.qxButtonActice = false;
          } else if (this.orderStatus == "2") {
            this.orderStatus = 3;
            this.zzButtonActice = true;
            this.fhButtonActice = false;
            this.xgButtonActice = false;
            this.qxButtonActice = false;
          } else if (this.orderStatus == "3") {
            this.orderStatus = 4;
            this.zzButtonActice = true;
            this.fhButtonActice = false;
            this.xgButtonActice = false;
            this.qxButtonActice = false;
          } else if (this.orderStatus == "8") {
            this.orderStatus = 5;
            this.zzButtonActice = true;
            this.fhButtonActice = false;
            this.xgButtonActice = false;
            this.qxButtonActice = false;
          } else if (this.orderStatus == "10") {
            this.orderStatus = 4;
            this.zzButtonActice = true;
            this.fhButtonActice = false;
            this.xgButtonActice = false;
            this.qxButtonActice = false;
          } else {
            this.orderStatus = 5;
            this.zzButtonActice = true;
            this.fhButtonActice = false;
            this.xgButtonActice = false;
            this.qxButtonActice = false;
          }

          this.baseOrderInfoData = [
            {
              timeXd: this.rTime(data.order[0].timeXd),
              deliverTime: this.rTime(data.order[0].deliverTime),
              reciveTime: this.rTime(data.order[0].reciveTime),
              autorecive: this.rTime(data.order[0].autorecive),
              totalJF: data.totalJF,
            },
          ];
          for(var i=0;i<data.order.length;i++){
            data.order[0].paymentTime = this.rTime(data.order[0].paymentTime);
          }
          this.orderInfoData = data.order;
          let priceYFK =
            data.pricezj -
            (data.order[0].freight +
              data.order[0].discountScore +
              data.order[0].discountCoupon);
          this.priceData = [
            {
              freight: data.order[0].freight,
              discountScore: data.order[0].discountScore,
              discountCoupon: data.order[0].discountCoupon,
              pricezj: data.pricezj,
              priceYf: priceYFK,
            },
          ];
          this.bookData = data.books;
          //唤醒进度条
          this.stepActive = true;
        } else {
          this.orderInfoData = [];
        }
        this.dataListLoading = false;
      });
    },
    getRecordList() {
      this.$http({
        url: this.$http.adornUrl(`/order/record/records/${this.orderId}`),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          for(var i=0;i<data.records.length;i++){
            data.records[i].time = this.rTime(data.records[i].time);
          }
          this.recordData = data.records;
          this.recordData.forEach((item) => {
            item.user = "后台管理员";
            item.status == "0"
              ? (item.status = "待付款")
              : item.status == "1"
              ? (item.status = "待发货")
              : item.status == "2"
              ? (item.status = "待收货")
              : item.status == "3"
              ? (item.status = "已收货")
              : item.status == "4"
              ? (item.status = "申请退货")
              : item.status == "5"
              ? (item.status = "待退货")
              : item.status == "6"
              ? (item.status = "拒绝退货")
              : item.status == "7"
              ? (item.status = "退货退款完成")
              : item.status == "8"
              ? (item.status = "已关闭")
              : item.status == "9"
              ? (item.status = "已取消")
              : item.status == "10"
              ? (item.status = "待评价")
              : (item.status = "订单异常");
          });
        } else {
          this.recordData = [];
        }
      });
    },
    orderFH(){
        this.fhVisible = true;
    },
     //修改时间格式
    rTime(date) {
    var json_date = new Date(date).toJSON();
    return new Date(new Date(json_date) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '') 
    },
    qrfh(wldh){
          this.$http({
            url: this.$http.adornUrl(
              `/order/orderinfo/deliver`
            ),
            method: 'post',
            data: this.$http.adornData({
              id: this.orderId,
              logisticsNum: wldh,
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.fhVisible = false;
                  location.reload();
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        
      
    }

    // // 新增 / 修改
    // addOrUpdateHandle (id) {
    //   this.addOrUpdateVisible = true
    //   this.$nextTick(() => {
    //     this.$refs.addOrUpdate.init(id)
    //   })
    // },
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
