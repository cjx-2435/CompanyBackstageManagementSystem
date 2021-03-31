<template>
  <div>
    <!-- 导航栏 -->
    <div class="options">
      <el-menu
        default-active="1"
        class="el-menu-demo"
        mode="horizontal"
        @select="handleSelect"
      >
        <el-menu-item index="1">商品</el-menu-item>
        <el-menu-item index="2">推荐</el-menu-item>
        <el-menu-item index="3">优惠</el-menu-item>
        <el-menu-item index="4">订单</el-menu-item>
        <el-menu-item index="5">会员</el-menu-item>
      </el-menu>
    </div>
    <!-- 模块显示 -->
    <product :realData="realData" v-show="view == 1">产品</product>
    <recommend v-show="view == 2"></recommend>
    <discount v-show="view == 3"></discount>
    <order v-show="view == 4"></order>
    <member v-show="view == 5"></member>
  </div>
</template>

<script>
import product from "@/components/shop-manage/product";
import recommend from "@/components/shop-manage/recommend";
import discount from "@/components/shop-manage/discount";
import order from "@/components/shop-manage/order";
import member from "@/components/shop-manage/member";

export default {
  data() {
    return {
      //tab栏切换
      view: 1,
      realData: [
        {
          num: 1,
          product_name: "试纸1",
          img:
            "https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg",
          spec: "25条/盒",
          desc:
            '<p><img src="https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg"/></p><p><br/></p><p>测试商品详情</p><p><br/></p>',
          price: "1000元",
          inventory: 999,
          from: "胶体金免疫分析仪",
          status: 1,
        },
        {
          num: 2,
          product_name: "滤纸1",
          img: "",
          spec: "25片/盒",
          desc: "",
          price: "1000元",
          inventory: 999,
          from: "新风系统",
          status: 1,
        },
        {
          num: 3,
          product_name: "滤纸2",
          img: "",
          spec: "25片/盒",
          desc: "",
          price: "1000元",
          inventory: 999,
          from: "新风系统",
          status: 0,
        },
        {
          num: 4,
          product_name: "试纸2",
          img: "",
          spec: "25条/盒",
          desc: "aaa",
          price: "1000元",
          inventory: 999,
          from: "胶体金免疫分析仪",
          status: 0,
        },
      ],
    };
  },
  components: {
    product,
    recommend,
    discount,
    order,
    member,
  },
  methods: {
    handleSelect(e) {
      this.view = e;
    },
  },
  mounted() {
  },
};
</script>

<style lang="scss">
.msg {
  width: auto;
}
.cell {
  text-align: center;
}
.is-sortable {
  .cell {
    text-indent: 24px;
  }
}
.el-image {
  img {
    width: 100%;
    height: 100%;
  }
  .image-slot {
    font-size: 100px;
    line-height: 100px;
  }
}
</style>