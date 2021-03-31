<template>
  <div>
    <div class="options">
      <div class="search">
        <el-input
          v-model="input"
          size="medium"
          placeholder="请输入账号或用户"
        ></el-input>
        <el-button @click="search" type="primary" icon="el-icon-search"
          >搜索</el-button
        >
      </div>
      <div class="filter">
        <div class="time">
          <h6>时间</h6>
          <el-date-picker
            v-model="time"
            type="datetimerange"
            :picker-options="pickerOptions"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            align="right"
          >
          </el-date-picker>
        </div>
        <div class="small">
          <h6>地区</h6>
          <el-select class="select" v-model="area" placeholder="请选择">
            <el-option
              v-for="item in areas"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
          <h6>性别</h6>
          <el-select class="select" v-model="sex" placeholder="请选择">
            <el-option
              v-for="item in sexes"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
          <h6>类别</h6>
          <el-select class="select" v-model="level" placeholder="请选择">
            <el-option
              v-for="item in levels"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </div>
      </div>
      <el-button @click="filter" icon="el-icon-s-operation" type="primary">筛选</el-button>
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
          prop="name"
          label="用户名"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="src"
          label="来源"
          sortable
        >
        </el-table-column>
        <el-table-column cell-class-name="cell" prop="tel" label="联系电话">
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="level"
          label="类别"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="account"
          label="账号"
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
          prop="sex"
          label="性别"
          sortable
        >
        </el-table-column>
        <el-table-column
          cell-class-name="cell"
          prop="area"
          label="地区"
          sortable
        >
        </el-table-column>
        <el-table-column label="账号状态">
          <template slot-scope="scope">
            <el-button
              :type="scope.row.status === 0 ? 'danger' : 'success'"
              @click="change(scope.row)"
              size="medium"
              >{{ scope.row.status === 0 ? "禁用" : "启用" }}</el-button
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
  </div>
</template>

<script>
import { getAreaInfo } from "@/utils/area";
export default {
  data() {
    return {
      input: "",
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
      time: [],
      //地区
      areas: [],
      //地区值
      area: "",
      //性别
      sexes: [
        {
          value: "0",
          label: "-",
        },
        {
          value: "1",
          label: "男",
        },
        {
          value: "2",
          label: "女",
        },
      ],
      //性别值
      sex: "",
      //类别
      levels: [
        {
          value: "0",
          label: "-",
        },
        {
          value: "1",
          label: "注册用户",
        },
        {
          value: "2",
          label: "微信用户",
        },
        {
          value: "3",
          label: "购买用户",
        },
      ],
      //类别值
      level: "",
      //表格数据
      tableData: [],
      //真实数据
      realData: [
        {
          num: 1,
          name: "用户名1",
          src: "小程序",
          creat_time: "2016-05-02",
          level: "注册用户",
          tel: "13955554444",
          account: "XF-002",
          sex: "男",
          area: "浙江",
          status: 0,
          remark: "",
        },
        {
          num: 2,
          name: "用户名2",
          src: "小程序",
          creat_time: "2016-05-02",
          level: "注册用户",
          tel: "13955554444",
          account: "XF-002",
          sex: "男",
          area: "浙江",
          status: 0,
          remark: "",
        },
        {
          num: 3,
          name: "用户名3",
          src: "小程序",
          creat_time: "2016-05-02",
          level: "注册用户",
          tel: "13955554444",
          account: "XF-002",
          sex: "男",
          area: "浙江",
          status: 0,
          remark: "",
        },
        {
          num: 4,
          name: "用户名4",
          src: "小程序",
          creat_time: "2016-05-02",
          level: "注册用户",
          tel: "13955554444",
          account: "XF-002",
          sex: "男",
          area: "浙江",
          status: 0,
          remark: "",
        },
      ],
      //每页显示的条数
      page_size: 10,
      //当前页码
      currentPage: 1,
      //显示表格数据
    };
  },
  mounted() {
    getAreaInfo((str) => {
      let sheng = str.filter((item) => {
        return item.di == "00";
      });
      let areas = sheng.map((ele) => {
        return {
          value: ele.code,
          label: ele.name,
        };
      });

      this.areas = areas;
    });
    this.tableData = this.realData.slice(0, this.page_size);
  },
  methods: {
    search() {
      console.log(this.input);
    },
    filter() {
      console.log(this.time);
      console.log(this.area);
      console.log(this.sex);
      console.log(this.level);
    },
    //页码变化
    handleCurrentChange(e) {
      this.tableData = this.realData.slice(
        (e - 1) * this.page_size,
        (e - 1) * this.page_size + 2
      );
    },
    change(e) {
      e.status === 0 ? (e.status = 1) : (e.status = 0);
      console.log(e);
    },
  },
};
</script>

<style lang="scss" scoped>
.options {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  height: 100px;
  .search {
    display: flex;
    width: 400px;
    button {
      margin: 0 15px;
    }
  }
  .filter {
    display: flex;
    width: 530px;
    flex-wrap: wrap;
    h6 {
      margin: 0 15px;
      font-size: 16px;
      line-height: 36px;
    }
    .time,
    .small {
      display: flex;
    }
    .time {
      padding-bottom: 10px;
      .el-date-editor {
        width: 453px;
      }
    }
    .small {
      .select {
        width: 110px;
      }
    }
  }
}
</style>