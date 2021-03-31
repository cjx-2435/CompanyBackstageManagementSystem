<template>
  <div>
    <div class="options">
      <el-input
        v-model="input"
        size="medium"
        placeholder="请输入账户/单位"
      ></el-input>
      <el-button @click="search" type="primary" icon="el-icon-search"
        >搜索</el-button
      >
      <div class="block">
        <el-date-picker
          v-model="value"
          type="datetimerange"
          :picker-options="pickerOptions"
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          align="right"
        >
        </el-date-picker>
      </div>
      <el-button @click="select" type="primary"
        >筛选</el-button
      >
    </div>
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
          prop="id"
          label="账户ID"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="unit"
          label="单位"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="creat_time"
          label="创建时间"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="customer"
          label="联系人"
          sortable
        >
        </el-table-column>
        <el-table-column cell-class-name="cell" prop="tel" label="联系电话">
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="seller"
          label="销售员"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="machine"
          label="绑定仪器"
          sortable
        >
        </el-table-column>
        <el-table-column label="密码管理">
          <template slot-scope="scope">
            <el-button @click="resetPSW(scope.row)" type="text" size="medium"
              >重置</el-button
            >
          </template>
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="run_time"
          label="运行时间"
          sortable
        >
        </el-table-column>
        <el-table-column label="维护记录">
          <template slot-scope="scope">
            <el-button @click="check(scope.row)" type="text" size="medium"
              >查看</el-button
            >
          </template>
        </el-table-column>
        <el-table-column cell-class-name="cell" prop="remark" label="备注">
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
    <el-dialog title="维护记录" :visible.sync="record">
      <div class="btn"><el-button icon="el-icon-plus" @click="addRecord">添加</el-button></div>
      <el-table :data="gridData">
        <el-table-column
          property="num"
          label="序号"
        ></el-table-column>
        <el-table-column
          property="time"
          label="维护时间"
        ></el-table-column>
        <el-table-column
          property="content"
          label="维护内容"
        ></el-table-column>
        <el-table-column
          property="handler"
          label="维护人"
        ></el-table-column>
        <el-table-column
          property="remark"
          label="备注"
        ></el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      //控制对话框是否显示
      record: false,
      //维修记录
      gridData: [
        {
          num: 1,
          time: "2020年12月7日",
          content: "维护内容 测试1111",
          handler: "郑小明",
          remark: "备注信息111",
        },
      ],
      //搜索输入
      input: "",
      //时间选择器快捷选项
      pickerOptions: {
        shortcuts: [
          {
            text: "最近一周",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "最近一个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "最近三个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit("pick", [start, end]);
            },
          },
        ],
      },
      //时间值
      value: "",
      //每页显示的条数
      page_size: 10,
      //当前页码
      currentPage: 1,
      //显示表格数据
      tableData: [],
      //真实存在的数据
      realData: [
        {
          num: 1,
          id: "WHF456",
          unit: "台州第一中学",
          creat_time: "2016-05-02",
          customer: "王小虎",
          tel: "13955554444",
          seller: "郑大明",
          machine: 10,
          run_time: "100小时",
          remark: "2020年11月27日已进行电话沟通",
        },
        {
          num: 2,
          id: "WHF456",
          unit: "台州第一中学",
          creat_time: "2016-05-04",
          customer: "王小虎",
          tel: "13955554444",
          seller: "郑大明",
          machine: 10,
          run_time: "100小时",
          remark: "2020年11月27日已进行电话沟通",
        },
        {
          num: 3,
          id: "WHF456",
          unit: "台州第一中学",
          creat_time: "2016-05-01",
          customer: "王小虎",
          tel: "13955554444",
          seller: "郑大明",
          machine: 10,
          run_time: "100小时",
          remark: "2020年11月27日已进行电话沟通",
        },
        {
          num: 4,
          id: "WHF456",
          unit: "台州第一中学",
          creat_time: "2016-05-03",
          customer: "王小虎",
          tel: "13955554444",
          seller: "郑大明",
          machine: 10,
          run_time: "100小时",
          remark: "2020年11月27日已进行电话沟通",
        },
      ],
    };
  },
  mounted() {
    this.tableData = this.realData.slice(0, this.page_size);
  },
  methods: {
    //重置密码
    resetPSW(row) {
      console.log(row);
    },
    //搜索
    search() {
      console.log(this.input);
    },
    //添加维护记录
    addRecord(){
      console.log("添加记录");
    },
    //筛选
    select() {
      console.log(this.value);
    },
    //页码变化
    handleCurrentChange(e) {
      this.tableData = this.realData.slice(
        (e - 1) * this.page_size,
        (e - 1) * this.page_size + 2
      );
    },
    //查看维护记录
    check(e) {
      this.record = !this.record;
    },
  },
};
</script>

<style lang="scss" scoped> 
.btn{
  padding-bottom: 15px;
  padding-right: 15px;
  display: flex;
  justify-content: flex-end;
}
.options {
  display: flex;
  .el-input {
    width: 230px;
  }
  button {
    margin: 0 15px;
  }
}
.cell {
  text-align: center;
}
</style>