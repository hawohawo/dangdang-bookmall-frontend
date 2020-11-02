<template>
  <div class="mod-demo-echarts">
    <el-alert title="提示：截止当前时间数据" type="warning" :closable="false">
      <div slot-scope="description"></div>
    </el-alert>
     <div class="chart-boxs">
        <el-row :gutter="12">
          <el-col :span="8">
            <el-card shadow="always">
              <el-col :span="8"><img src="/static/img/image_ico/orderm.png" class="box_img"></el-col>
              <el-col :span="8">
                <div class="box_name">{{monthN}}</div>
                <div class="box_num">{{monthV}}</div>
               </el-col>
            </el-card>
          </el-col>
          <el-col :span="8">
            <el-card shadow="always">
              <el-col :span="8"><img src="/static/img/image_ico/money.png" class="box_img"></el-col>
              <el-col :span="8">
                <div class="box_name">{{totalN}}</div>
                <div class="box_num">￥{{totalV}}</div>
               </el-col>
            </el-card>
          </el-col>
          <el-col :span="8">
            <el-card shadow="always">
              <el-col :span="8"><img src="/static/img/image_ico/moneys.png" class="box_img"></el-col>
              <el-col :span="8">
                <div class="box_name">{{todayN}}</div>
                <div class="box_num">￥{{todayV}}</div>
               </el-col>
            </el-card>
          </el-col>

        </el-row>
        <el-row :gutter="12">
          <el-col :span="8">
            <el-card shadow="always">
              <el-col :span="8"><img src="/static/img/image_ico/offshelf.png" class="bo
              x_img"></el-col>
              <el-col :span="8">
                <div class="box_name">{{offShelfN}}</div>
                <div class="box_num">{{offShelfV}}</div>
               </el-col>
            </el-card>
          </el-col>
          <el-col :span="8">
            <el-card shadow="always">
              <el-col :span="8"><img src="/static/img/image_ico/onshelf.png" class="box_img"></el-col>
              <el-col :span="8">
                <div class="box_name">{{onShelfN}}</div>
                <div class="box_num">{{onShelfV}}</div>
               </el-col>
            </el-card>
          </el-col>
          <el-col :span="8">
            <el-card shadow="always">
              <el-col :span="8"><img src="/static/img/image_ico/all.png" class="box_img"></el-col>
              <el-col :span="8">
                <div class="box_name">全部商品数</div>
                <div class="box_num">{{offShelfV+onShelfV}}</div>
               </el-col>
            </el-card>
          </el-col>

        </el-row>
      </div>
      <el-col :span="24">
        <el-card>
          <div id="MonthSole" class="chart-box"></div>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card>
          <div id="WeekSole" class="chart-box"></div>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card>
          <div id="MonthOrderSole" class="chart-box"></div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import echarts from "echarts";
export default {
  data() {
    return {
      chartLine: null,
      chartWeekLine:null,
      chartMonthLine:null,
      monthN:'',
      monthV:'',
      totalN:'',
      totalV:'',
      todayN:'',
      todayV:'',
      offShelfN:'',
      offShelfV:'',
      onShelfN:'',
      onShelfV:'',
      //月销售量统计（本月与上月）
      monthOrderOption: {
          title: {
              text: '月销售量统计',
              subtext: '数据来源:DangDangMall',
              left: 'center'
          },
          tooltip: {
              trigger: 'item',
              formatter: '{a} <br/>{b} : {c} ({d}%)'
          },
          legend: {
              orient: 'vertical',
              left: 'left'
          },
          series: [
              {
                  name: '数据来源:DangDangMall',
                  type: 'pie',
                  radius: '55%',
                  center: ['50%', '60%'],
                  data: [
                      {value: 335, name: '直接访问'},
                      {value: 310, name: '邮件营销'}
                  ],
                  emphasis: {
                      itemStyle: {
                          shadowBlur: 10,
                          shadowOffsetX: 0,
                          shadowColor: 'rgba(0, 0, 0, 0.5)'
                      }
                  }
              }
          ]
      },
      //周订单量
      weekOption :{
          backgroundColor: '#fff',
          title: {
              text: '周销量统计表',
              left: 'center',
              top: 20,
              textStyle: {
                  color: '#ccc'
              }
          },

          tooltip: {
              trigger: 'item',
              formatter: '{a} <br/>{b} : {c} ({d}%)'
          },

          visualMap: {
              show: false,
              min: 80,
              max: 600,
              inRange: {
                  colorLightness: [0, 1]
              }
          },
          series: [
              {
                  name: '数据来自DangDangMall',
                  type: 'pie',
                  radius: '55%',
                  center: ['50%', '50%'],
                  data: [
                      {name: '本周',value: 335},
                      {name: '上周',value: 310}
                  ].sort(function (a, b) { return a.value - b.value; }),
                  roseType: 'radius',
                  label: {
                      color: '#000'
                  },
                  labelLine: {
                      lineStyle: {
                          color: 'rgba(255, 255, 255, 0.3)'
                      },
                      smooth: 0.2,
                      length: 10,
                      length2: 20
                  },
                  itemStyle: {
                      color: '#c23531',
                      shadowBlur: 200,
                      shadowColor: 'rgba(0, 0, 0, 0.5)'
                  },

                  animationType: 'scale',
                  animationEasing: 'elasticOut',
                  animationDelay: function (idx) {
                      return Math.random() * 200;
                  }
              }
          ]
      },
      //当月销量排名
      srotmOption : {
        title: {
          text: "当月销量统计",
          subtext: "数据来自DangDangMall",
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow",
          },
        },
        legend: {
          data: ["销量"],
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true,
        },
        xAxis: {
          type: "value",
          boundaryGap: [0, 0.01],
        },
        yAxis: {
          type: "category",
          data: ["123","123"]
        },
        series: [
          {
            type: "bar",
            data: [1,2],
          },
        ],
      },
    };
  },
  mounted() {
    this.initChartLine();
    this.initChartWeekLine();
    this.initChartMonthLine();
  },
  activated() {
    // 由于给echart添加了resize事件, 在组件激活时需要重新resize绘画一次, 否则出现空白bug
    if (this.chartLine) {
      this.chartLine.resize();
    };
    if (this.chartWeekLine) {
      this.chartWeekLine.resize();
    };
    if (this.chartMonthLine) {
      this.chartMonthLine.resize();
    };
    this.getDataSrotm();
    this.getDataWeek();
    this.getDataMonthOrder();
    this.getOrderToBeShipped();
    this.getAllSales();
    this.getDaySales();
    this.getTotalOffShelves();
    this.getTotalOnShelves();
  },
  methods: {
    // 本月销量统计
    initChartLine() {
      this.chartLine = echarts.init(document.getElementById("MonthSole"));
      this.chartLine.setOption(this.srotmOption);
      window.addEventListener("resize", () => {
        this.chartLine.resize();
      });
    },
    //本周销量统计
    initChartWeekLine() {
      this.chartWeekLine = echarts.init(document.getElementById("WeekSole"));
      this.chartWeekLine.setOption(this.weekOption);
      window.addEventListener("resize", () => {
        this.chartWeekLine.resize();
      });
    },
     //本月销量统计(本月与上月)
    initChartMonthLine() {
      this.chartMonthLine = echarts.init(document.getElementById("MonthOrderSole"));
      this.chartMonthLine.setOption(this.monthOrderOption);
      window.addEventListener("resize", () => {
        this.chartMonthLine.resize();
      });
    },
    // 获取数据列表
    getDataSrotm() {
      this.$http({
        url: this.$http.adornUrl("/statistics/srotm"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.srotmOption.yAxis.data = data.bookNameDate;
          this.srotmOption.series[0].data = data.bookNumDate;
          var myDate = new Date();
          this.srotmOption.title.subtext = "数据来自DangDangMall 更新时间："+ myDate.toLocaleString();
          this.initChartLine();
        } else {
        
        }
      });
    },
    //获取周数据
    getDataWeek() {
      this.$http({
        url: this.$http.adornUrl("/statistics/week/order"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.weekOption.series[0].data = data.weekorder;
          var myDate = new Date();
          this.weekOption.series[0].name = "数据来自DangDangMall 更新时间："+ myDate.toLocaleString();
          this.initChartWeekLine();
        } else {
        }
      });
    },
    //获取月销量数据
    getDataMonthOrder() {
      this.$http({
        url: this.$http.adornUrl("/statistics/month/order"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.monthOrderOption.series[0].data = data.monthorder;
          var myDate = new Date();
          this.monthOrderOption.title.subtext = "数据来自DangDangMall 更新时间："+ myDate.toLocaleString();
          this.initChartMonthLine();
        } else {
        }
      });
    },
    //待发货订单量统计
    getOrderToBeShipped() {
      this.$http({
        url: this.$http.adornUrl("/statistics/order/shipped"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.monthN = data.ordershipped.name;
          this.monthV = data.ordershipped.value;
        } else {
        }
      });
    },
    //总销售额
    getAllSales() {
      this.$http({
        url: this.$http.adornUrl("/statistics/all/sales"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.totalN = data.allsales.name;
          this.totalV = data.allsales.value;
        } else {
        }
      });
    },
    //今日销售额
    getDaySales() {
      this.$http({
        url: this.$http.adornUrl("/statistics/day/sales"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.todayN = data.daysales.name;
          this.todayV = data.daysales.value;
        } else {
        }
      });
    },
    //下架总数统计
    getTotalOffShelves() {
      this.$http({
        url: this.$http.adornUrl("/product/baseinfo/off/shelves"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.offShelfN = data.offshelves.name;
          this.offShelfV = data.offshelves.value;
        } else {
        }
      });
    },
    //上架总数统计
     getTotalOnShelves() {
      this.$http({
        url: this.$http.adornUrl("/product/baseinfo/on/shelves"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.onShelfN = data.onshelves.name;
          this.onShelfV = data.onshelves.value;
        } else {
        }
      });
    },
  },
  
};
</script>

<style lang="scss">
.mod-demo-echarts {
  > .el-alert {
    margin-bottom: 10px;
  }
  > .el-row {
    margin-top: -10px;
    margin-bottom: -10px;
    .el-col {
      padding-top: 10px;
      padding-bottom: 10px;
    }
  }
  .chart-box {
    min-height: 400px;
  }
  .chart-boxs {
    min-height: 300px;
  }
}
.el-row {
    margin-bottom: 20px;
    &:last-child {
      margin-bottom: 0;
    }
  }
  .el-col {
    border-radius: 4px;
  }
  .bg-purple-dark {
    background: #99a9bf;
  }
  .bg-purple {
    background: #d3dce6;
  }
  .bg-purple-light {
    background: #e5e9f2;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }
  .box_name{
    font-size: 16px;
    font-weight: 600;
    color: #000;
    margin: 10px;
  }
  .box_num{
    font-size: 16px;
    font-weight: 600;
    color: #ccc;
    margin-left: 10px;
  }
  .box_img{
    margin-bottom: 20px;
    margin-left: 50px;
  }
</style>

