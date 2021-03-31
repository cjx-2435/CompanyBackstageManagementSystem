<template>
  <div class="container">
    <div class="options">
      <el-input
        v-model="input"
        size="medium"
        placeholder="请输入商品名"
      ></el-input>
      <el-button @click="search" type="primary" icon="el-icon-search"
        >搜索</el-button
      >
      <el-button class="add" @click="add" type="default" icon="el-icon-plus"
        >添加</el-button
      >
    </div>

    <!-- 商品添加 -->
    <el-dialog
      title="上架商品"
      :close-on-click-modal="false"
      :visible.sync="dialogFormVisible"
      @close="reset"
    >
      <el-form :model="form">
        <el-form-item label="商品编号" :label-width="formLabelWidth">
          <div>{{ form.num }}</div>
        </el-form-item>
        <el-form-item label="商品名称" :label-width="formLabelWidth">
          <el-input class="input" v-model="form.name" />
        </el-form-item>
        <el-form-item label="商品分类" :label-width="formLabelWidth">
          <el-select class="select" v-model="form.sys" placeholder="体系分类">
            <el-option
              v-for="item in syses"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
          <el-select
            class="select"
            v-model="form.system"
            placeholder="系统分类"
          >
            <el-option
              v-for="item in systems"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
          <el-select
            class="select"
            v-model="form.classify1"
            placeholder="一级分类"
          >
            <el-option
              v-for="item in classes1"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
          <el-select
            class="select"
            v-model="form.classify2"
            placeholder="二级分类"
          >
            <el-option
              v-for="item in classes2"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
          <el-select
            class="select"
            v-model="form.classify3"
            placeholder="三级分类"
          >
            <el-option
              v-for="item in classes3"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="商品定价" :label-width="formLabelWidth">
          <el-input class="input" v-model="form.price">
            <template slot="append">元</template>
          </el-input>
        </el-form-item>
        <el-form-item label="售卖价格" :label-width="formLabelWidth">
          <el-input class="input" v-model="form.sell_price">
            <template slot="append">元</template>
          </el-input>
        </el-form-item>
        <el-form-item label="活动折扣" :label-width="formLabelWidth">
          <el-tooltip content="可使用优惠券额度" placement="top">
            <el-input class="input" v-model="form.discount" />
          </el-tooltip>
        </el-form-item>
        <el-form-item label="商品库存" :label-width="formLabelWidth">
          <el-input class="input" v-model="form.inventory" />
        </el-form-item>
        <el-form-item
          label="商品图片"
          class="img_label"
          :label-width="formLabelWidth"
        >
          <el-upload
            class="img"
            action="#"
            ref="imgUpload"
            :on-change="changed"
            :limit="upload_num"
            list-type="picture-card"
            :auto-upload="false"
          >
            <i slot="default" class="el-icon-plus"></i>
            <div slot="file" slot-scope="{ file }">
              <img
                class="el-upload-list__item-thumbnail"
                :src="file.url"
                alt=""
              />
              <span class="el-upload-list__item-actions">
                <span
                  class="el-upload-list__item-preview"
                  @click="handlePictureCardPreview(file)"
                >
                  <i class="el-icon-zoom-in"></i>
                </span>
                <span
                  v-if="!disabled"
                  class="el-upload-list__item-delete"
                  @click="handleRemove(file)"
                >
                  <i class="el-icon-delete"></i>
                </span>
              </span>
            </div>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible" append-to-body>
            <img width="100%" :src="dialogImageUrl" alt="" />
          </el-dialog>
        </el-form-item>
        <el-form-item label="商品详情" :label-width="formLabelWidth">
          <ueditor></ueditor>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="submit">确 定</el-button>
      </div>
    </el-dialog>

    <!-- 商品列表 -->
    <div class="table">
      <el-table
        :data="tableData"
        style="width: 100%"
        :default-sort="{ prop: 'num', order: 'ascending' }"
      >
        <el-table-column
          cell-class-name="cell"
          prop="num"
          label="序号"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="product_name"
          label="商品名称"
          sortable
        >
        </el-table-column>
        <el-table-column label="商品图片" prop="img">
          <template class="block" slot-scope="scope">
            <el-image
              style="width: 100px; height: 100px"
              :src="scope.row.img"
              fit="cover"
            >
              <div slot="error" class="image-slot">
                <i class="el-icon-picture-outline"></i>
              </div>
            </el-image>
          </template>
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="spec"
          label="规格"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="price"
          label="价格"
          sortable
        >
        </el-table-column>
        <el-table-column label="商品介绍">
          <template slot-scope="scope">
            <el-button
              @click="getDesc(scope.row.desc)"
              type="text"
              size="medium"
              >详情</el-button
            >
          </template>
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="inventory"
          label="库存"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          class="sort"
          prop="from"
          label="归属"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="status"
          label="状态"
          sortable
        >
          <template slot-scope="scope">
            <span>{{ scope.row.status == 1 ? "发布" : "未发布" }}</span>
          </template>
        </el-table-column>
        <el-table-column>
          <template slot-scope="scope">
            <el-button
              type="primary"
              icon="el-icon-edit"
              @click="edit(scope.row)"
              circle
            ></el-button>
          </template>
        </el-table-column>
        <el-table-column>
          <template slot-scope="scope">
            <el-button
              type="danger"
              icon="el-icon-delete"
              @click="del(scope.row)"
              circle
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="block">
      <el-pagination
        @current-change="handleCurrentChange"
        :current-page.sync="currentPage"
        :page-size="page_size"
        layout="total, prev, pager, next"
        :total="realData.length"
      >
      </el-pagination>
    </div>
  </div>
</template>

<script>
import ueditor from "@/components/ueditor";
export default {
  data() {
    return {
      //搜索输入
      input: "",
      dialogFormVisible: false,
      formLabelWidth: "90px",
      //体系分类条目
      syses: [
        {
          value: 0,
          label: "测试1",
        },
        {
          value: 1,
          label: "测试2",
        },
      ],
      //系统分类条目
      systems: [
        {
          value: 0,
          label: "测试1",
        },
        {
          value: 1,
          label: "测试2",
        },
      ],
      //分类1条目
      classes1: [
        {
          value: 0,
          label: "测试1",
        },
        {
          value: 1,
          label: "测试2",
        },
      ],
      //分类2条目
      classes2: [
        {
          value: 0,
          label: "测试1",
        },
        {
          value: 1,
          label: "测试2",
        },
      ],
      //分类3条目
      classes3: [
        {
          value: 0,
          label: "测试1",
        },
        {
          value: 1,
          label: "测试2",
        },
      ],
      form: {
        //商品编号
        num: "001",
        //商品名称
        name: "",
        //商品定价
        price: "",
        //售卖价格
        sell_price: "",
        //折扣
        discount: "",
        //商品库存
        inventory: "",
        //体系分类值
        sys: "",
        //系统分类值
        system: "",
        //一级分类值
        classify1: "",
        classify2: "",
        classify3: "",
        imgs: [],
      },

      dialogImageUrl: "",
      dialogVisible: false,
      disabled: false,
      upload_num: 6,
      //每页显示的条数
      page_size: 5,
      //当前页码
      currentPage: 1,
      //显示表格数据
      tableData: [],
      // //真实存在的数据
      // realData: [],
    };
  },
  props: {
    realData: {
      type: Array,
    },
  },
  components: {
    ueditor,
  },
  mounted() {
    this.tableData = this.realData.slice(0, this.page_size);
  },
 
  beforeDestroy(){
    console.log("组件销毁");
  },
  methods: {
    //搜索
    search() {
      console.log(this.input);
    },
    //商品上架表单显示
    add() {
      this.dialogFormVisible = !this.dialogFormVisible;
    },
    //移除图片
    handleRemove(file) {
      let index = this.form.imgs.findIndex((item) => item == file);
      this.form.imgs.splice(index, 1);
    },
    //打开预览图
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    //提交表单
    submit() {
      console.log(this.form);
    },
    //上传图片有变动时触发
    changed(f, list) {
      this.form.imgs = list;
    },
    //表单关闭清空数据
    reset() {
      for (let i in this.form) {
        if (i == "imgs") {
          this.form[i].splice(0, this.form[i].length);
        } else if (i == "num") {
        } else {
          this.form[i] = "";
        }
      }
    },
    //页码变化
    handleCurrentChange(e) {
      this.tableData = this.realData.slice(
        (e - 1) * this.page_size,
        (e - 1) * this.page_size + 2
      );
    },
    getDesc(desc) {
      if (!desc) {
        this.$message({
          showClose: true,
          message: "商品详情数据为空",
          type: "error",
        });
        return;
      }

      this.$alert(desc, "商品详情", {
        customClass: "msg",
        dangerouslyUseHTMLString: true,
      });
    },
    edit(row) {
      console.log(row);
    },
    del(row) {
      console.log(row);
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  padding: 15px;
  .options {
    display: flex;
    .el-input {
      width: 230px;
    }
    button {
      margin: 0 15px;
    }
    .add {
      position: absolute;
      right: 0;
      transform: translateX(-50%);
    }
  }
}

.input {
  width: 200px;
}
.select {
  width: 120px;
}
.img /deep/ .el-upload,
.img /deep/ .el-upload-list__item {
  width: 100px;
  height: 100px;
}
.img /deep/ .el-upload--picture-card,
.img_label /deep/ .el-form-item__label {
  line-height: 100px;
}
</style>