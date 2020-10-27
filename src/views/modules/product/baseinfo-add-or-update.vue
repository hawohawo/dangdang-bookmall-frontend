<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-row>
      <el-col :span="24">
        <el-steps :active="active" finish-status="success" align-center>
          <el-step title="图书基本信息"></el-step>
          <el-step title="图书出版信息"></el-step>
          <el-step title="图书详情信息"></el-step>
        </el-steps>
      </el-col>
    </el-row>
    <el-divider></el-divider>
    <el-row v-if="firstStep">
      <el-col :span="24">
        <el-form
          :model="dataForm"
          :rules="dataRule"
          ref="dataForm"
          @keyup.enter.native="dataFormSubmit()"
          label-width="130px"
        >
          <el-row>
            <el-col :span="14">
              <el-form-item label="图书图片" prop="picture">
                <singleUpload v-model="dataForm.picture"></singleUpload>
              </el-form-item>
            </el-col>
            <el-col :span="10">
              <el-form-item label="是否立即上架" prop="insale">
                <el-switch
                  v-model="dataForm.insale"
                  active-color="#13ce66"
                  inactive-color="#ff4949"
                  :active-value="1"
                  :inactive-value="0"
                >
                </el-switch>
              </el-form-item>
            </el-col>
          </el-row>

          <el-row>
            <el-col :span="12">
              <el-form-item label="图书分类" prop="typeId">
                <el-select
                  v-model="dataForm.typeId"
                  placeholder="请选择分类"
                  style="width: 90%"
                >
                  <el-option
                    v-for="item in type"
                    :key="item.id"
                    :label="item.name"
                    :value="item.id"
                  >
                  </el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="图书名称" prop="name">
                <el-input
                  v-model="dataForm.name"
                  placeholder="图书名称"
                ></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-form-item label="副标题" prop="fname">
            <el-input v-model="dataForm.fname" placeholder="副标题"></el-input>
          </el-form-item>
          <el-form-item label="图书介绍" prop="introduce">
            <el-input
              v-model="dataForm.introduce"
              placeholder="图书介绍"
            ></el-input>
          </el-form-item>
          <el-form-item label="作者信息" prop="author">
            <el-input v-model="dataForm.author" placeholder="作者"></el-input>
          </el-form-item>
          <el-row>
            <el-col :span="12">
              <el-form-item label="图书评分" prop="score">
                <el-input-number
                  v-model="dataForm.score"
                  :min="0"
                  :max="5"
                  label="图书定价"
                >
                </el-input-number>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="图书定价" prop="priceDj">
                <el-input-number
                  v-model="dataForm.priceDj"
                  :min="0"
                  :max="999999"
                  label="图书定价"
                >
                </el-input-number>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12">
              <el-form-item label="图书售价" prop="priceSj">
                <el-input-number
                  v-model="dataForm.priceSj"
                  :min="0"
                  :max="999999"
                  label="图书售价"
                >
                </el-input-number>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="运费" prop="priceYf">
                <el-input-number
                  v-model="dataForm.priceYf"
                  :min="0"
                  :max="999999"
                  label="运费"
                >
                </el-input-number>
              </el-form-item>
            </el-col>
          </el-row>

          <el-form-item label="图书关键字" prop="keyword">
            <el-input
              v-model="dataForm.keyword"
              placeholder="图书关键字"
            ></el-input>
          </el-form-item>
          <el-row>
            <el-col :span="12">
              <el-form-item label="图书库存" prop="stock">
                <el-input-number
                  v-model="dataForm.stock"
                  :min="1"
                  :max="999999"
                  label="库存"
                ></el-input-number>
              </el-form-item>
            </el-col>

            <el-col :span="12">
              <el-form-item label="发放积分" prop="integral">
                <el-input-number
                  v-model="dataForm.integral"
                  :precision="2"
                  :step="0.1"
                  :max="400"
                ></el-input-number>
              </el-form-item>
            </el-col>
          </el-row>
          <el-form-item label="备注" prop="remarks">
            <el-input v-model="dataForm.remarks" placeholder="备注"></el-input>
          </el-form-item>
          <!-- <el-form-item label="出版信息ID" prop="publishId">
            <el-input
              v-model="dataForm.publishId"
              placeholder="出版信息ID"
            ></el-input>
          </el-form-item>
          <el-form-item label="图书详情ID" prop="bookdetailId">
            <el-input
              v-model="dataForm.bookdetailId"
              placeholder="图书详情ID"
            ></el-input>
          </el-form-item>
          <el-form-item label="评论ID" prop="commentId">
            <el-input
              v-model="dataForm.commentId"
              placeholder="评论ID"
            ></el-input>
          </el-form-item> -->
        </el-form>
      </el-col>
    </el-row>

    <el-row v-if="secondStep">
      <el-col :span="24">
        <el-form
          :model="publicDataForm"
          :rules="dataRule"
          ref="dataForm"
          label-width="130px"
          @keyup.enter.native="dataFormSubmit()"
        >
          <el-form-item label="出版社" prop="publisher">
            <el-input
              v-model="publicDataForm.publisher"
              placeholder="出版社"
            ></el-input>
          </el-form-item>
          <el-form-item label="出版时间" prop="publishTime">
            <el-date-picker
              v-model="publicDataForm.publishTime"
              type="date"
              placeholder="选择日期"
            >
            </el-date-picker>
          </el-form-item>
          <el-form-item label="ISBN" prop="isbn">
            <el-input
              v-model="publicDataForm.isbn"
              placeholder="ISBN"
            ></el-input>
          </el-form-item>
          <el-form-item label="字数" prop="wordNum">
            <el-input
              v-model="publicDataForm.wordNum"
              placeholder="字数"
            ></el-input>
          </el-form-item>
          <el-form-item label="开本" prop="size">
            <el-input
              v-model="publicDataForm.size"
              placeholder="开本"
            ></el-input>
          </el-form-item>
          <el-form-item label="纸张" prop="paperType">
            <el-input
              v-model="publicDataForm.paperType"
              placeholder="纸张"
            ></el-input>
          </el-form-item>
          <el-form-item label="包装" prop="pack">
            <el-input
              v-model="publicDataForm.pack"
              placeholder="包装"
            ></el-input>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>

    <el-row v-if="thirdStep">
      <el-col :span="24">
        <bd @getBdValue="getBdValue"></bd>
      </el-col>
    </el-row>

    <el-button
      v-if="preStepActice"
      style="margin-top: 12px; margin-left: 20px"
      @click="pre"
      >上一步</el-button
    >
    <el-button
      v-if="nextStepActice"
      style="margin-top: 12px; margin-left: 20px"
      @click="next"
      >下一步</el-button
    >
    <el-button
      v-if="addStepActice"
      style="margin-top: 12px; margin-left: 20px"
      @click="next"
      >添加图书</el-button
    >

    <!-- <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span> -->
  </el-dialog>
</template>

<script>
import singleUpload from "@/components/upload/singleUpload";
import bd from "@/components/upload/bd";

export default {
  components: { singleUpload: singleUpload, bd: bd },

  data() {
    return {
      nextStepActice: true,
      preStepActice: false,
      addStepActice: false,
      wangbd: "",
      visible: false,
      active: 1,
      firstStep: true,
      secondStep: false,
      thirdStep: false,
      type: "",
      dataForm: {
        id: 0,
        picture: "",
        typeId: "",
        name: "",
        fname: "",
        introduce: "",
        author: "",
        score: "",
        priceDj: "",
        priceSj: "",
        priceYf: "",
        insale: 0,
        keyword: "",
        stock: 1,
        integral: 0,
        remarks: "",
        publishId: "",
        bookdetailId: "",
        commentId: "",
      },
      publicDataForm: {
        id: 0,
        publisher: "",
        publishTime: "",
        isbn: "",
        wordNum: "",
        size: "",
        paperType: "",
        pack: "",
      },
      dataRule: {
        picture: [
          { required: true, message: "图书图片不能为空", trigger: "blur" },
        ],
        typeId: [
          { required: true, message: "图书分类ID不能为空", trigger: "blur" },
        ],
        name: [
          { required: true, message: "图书名称不能为空", trigger: "blur" },
        ],
        fname: [{ required: true, message: "副标题不能为空", trigger: "blur" }],
        introduce: [
          { required: true, message: "图书介绍不能为空", trigger: "blur" },
        ],
        author: [{ required: true, message: "作者不能为空", trigger: "blur" }],
        score: [
          { required: true, message: "图书评分不能为空", trigger: "blur" },
        ],
        priceDj: [
          { required: true, message: "图书定价不能为空", trigger: "blur" },
        ],
        priceSj: [
          { required: true, message: "图书售价不能为空", trigger: "blur" },
        ],
        priceYf: [{ required: true, message: "运费不能为空", trigger: "blur" }],
        insale: [
          {
            required: true,
            message: "是否上架（0：已上架 1：未上架）不能为空",
            trigger: "blur",
          },
        ],
        keyword: [
          { required: true, message: "图书关键字不能为空", trigger: "blur" },
        ],
        stock: [
          { required: true, message: "图书库存不能为空", trigger: "blur" },
        ],
        integral: [
          { required: true, message: "积分不能为空", trigger: "blur" },
        ],
        remarks: [{ required: true, message: "备注不能为空", trigger: "blur" }],
        publisher: [
          { required: true, message: "出版社不能为空", trigger: "blur" },
        ],
        publishTime: [
          { required: true, message: "出版时间不能为空", trigger: "blur" },
        ],
        isbn: [{ required: true, message: "ISBN不能为空", trigger: "blur" }],
        wordNum: [{ required: true, message: "字数不能为空", trigger: "blur" }],
        size: [{ required: true, message: "开本不能为空", trigger: "blur" }],
        paperType: [
          { required: true, message: "纸张不能为空", trigger: "blur" },
        ],
        pack: [{ required: true, message: "包装不能为空", trigger: "blur" }],
      },
    };
  },

  methods: {
    init(id) {
      this.getType();
      this.dataForm.id = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(
              `/product/baseinfo/info/${this.dataForm.id}`
            ),
            method: "get",
            params: this.$http.adornParams(),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.picture = data.baseinfo.picture;
              this.dataForm.typeId = data.baseinfo.typeId;
              this.dataForm.name = data.baseinfo.name;
              this.dataForm.fname = data.baseinfo.fname;
              this.dataForm.introduce = data.baseinfo.introduce;
              this.dataForm.author = data.baseinfo.author;
              this.dataForm.score = data.baseinfo.score;
              this.dataForm.priceDj = data.baseinfo.priceDj;
              this.dataForm.priceSj = data.baseinfo.priceSj;
              this.dataForm.priceYf = data.baseinfo.priceYf;
              this.dataForm.insale = data.baseinfo.insale;
              this.dataForm.keyword = data.baseinfo.keyword;
              this.dataForm.stock = data.baseinfo.stock;
              this.dataForm.integral = data.baseinfo.integral;
              this.dataForm.remarks = data.baseinfo.remarks;
              this.dataForm.publishId = data.baseinfo.publishId;
              this.dataForm.bookdetailId = data.baseinfo.bookdetailId;
              this.dataForm.commentId = data.baseinfo.commentId;
            }
          });
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/product/baseinfo/${!this.dataForm.id ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              picture: this.dataForm.picture,
              typeId: this.dataForm.typeId,
              name: this.dataForm.name,
              fname: this.dataForm.fname,
              introduce: this.dataForm.introduce,
              author: this.dataForm.author,
              score: this.dataForm.score,
              priceDj: this.dataForm.priceDj,
              priceSj: this.dataForm.priceSj,
              priceYf: this.dataForm.priceYf,
              insale: this.dataForm.insale,
              keyword: this.dataForm.keyword,
              stock: this.dataForm.stock,
              integral: this.dataForm.integral,
              remarks: this.dataForm.remarks,
              publishId: this.dataForm.publishId,
              bookdetailId: this.dataForm.bookdetailId,
              commentId: this.dataForm.commentId,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$emit("refreshDataList");
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
    getType() {
      this.$http({
        url: this.$http.adornUrl("/product/type/types"),
        method: "get",
        params: this.$http.adornParams({}),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.type = data.list;
        } else {
          this.type = "";
        }
      });
    },
    // 下一步骤
    next() {
      if (this.active++ > 2) {
        this.active = 1;
      }
      if (this.active == 1) {
        this.firstStep = true;
        this.secondStep = false;
        this.thirdStep = false;
        this.preStepActice = false;
        this.addStepActice = false;
        this.nextStepActice = true;
      }
      if (this.active == 2) {
        this.firstStep = false;
        this.secondStep = true;
        this.thirdStep = false;
        this.preStepActice = true;
        this.addStepActice = false;
        this.nextStepActice = true;
      }
      if (this.active == 3) {
        this.firstStep = false;
        this.secondStep = false;
        this.thirdStep = true;
        this.preStepActice = true;
        this.addStepActice = true;
        this.nextStepActice = false;
      }
    },
    // 上一步骤
    pre(){
      this.active--;
      if (this.active == 1) {
        this.firstStep = true;
        this.secondStep = false;
        this.thirdStep = false;
        this.preStepActice = false;
        this.addStepActice = false;
        this.nextStepActice = true;
      }
      if (this.active == 2) {
        this.firstStep = false;
        this.secondStep = true;
        this.thirdStep = false;
        this.preStepActice = true;
        this.addStepActice = false;
        this.nextStepActice = true;
      }
      if (this.active == 3) {
        this.firstStep = false;
        this.secondStep = false;
        this.thirdStep = true;
        this.preStepActice = true;
        this.addStepActice = true;
        this.nextStepActice = false;
      }
    },
    // 获取富文本值

    getBdValue(val) {
      this.wangbd = val;
      console.log("this.dataform==========="+ JSON.stringify(this.dataForm))
      console.log("this.dataform==========="+JSON.stringify(this.publicDataForm))

    },
  },
};
</script>

<style lang="scss">
.el-row {
  margin-bottom: 0px;
  &:last-child {
    margin-bottom: 0;
  }
}
.el-col {
  border-radius: 4px;
}
</style>
