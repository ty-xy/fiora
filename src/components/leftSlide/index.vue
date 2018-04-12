<template>
  <div class="left-side">
    <div class="left-side-inner">
      <router-link to="/" class="logo block imgset">
        <img src="./images/13logo.png" alt="AdminX" class="img">
        <img src="./images/logo2x.png" alt="AdminX" class="img1">
      </router-link>
      <el-menu
        class="menu-box"
        theme="dark"
        router
        :default-active="$route.path">
        <div
          v-for="(item, index) in nav_menu_data"
          :key="index">
          <el-menu-item
            class="menu-list"
            v-if="typeof item.child === 'undefined'"
            :index="item.path">
            <i class="icon fa" :class="item.icon"></i>
            <span v-text="item.title" class="text"></span>
          </el-menu-item>
          <el-submenu
            :index="item.path"
            v-else>
            <template slot="title">
              <i class="icon fa" :class="item.icon"></i>
              <span v-text="item.title" class="text"></span>
            </template>
            <el-menu-item
              class="menu-list"
              v-for="(sub_item, sub_index) in item.child"
              :index="sub_item.path"
              :key="sub_index">
              <!--<i class="icon fa" :class="sub_item.icon"></i>-->
              <span v-text="sub_item.title" class="text"></span>
            </el-menu-item>
          </el-submenu>
        </div>
      </el-menu>
    </div>
  </div>
</template>
<script type="text/javascript">
  export default{
    name: 'slide',
    data(){
      return {
        nav_menu_data: [{
          title: "出题",
          path: "/home",
          icon: "fa-telegram"
        },
        {
            title: "未审核",
            path: "/nocheck",
            icon: "fa-comment"
       },
         {
          title: "审核",
          path: "/check",
          icon: "fa-check-circle"
        },
        //  {
        //   title: "表格管理",
        //   path: "/table",
        //   icon: "fa-table",
        //   child: [{
        //     title: "基本表格",
        //     path: "/table/base"
        //   }, {
        //     title: "排序表格",
        //     path: "/table/sort"
        //   }]
        // }
        //  {
        //   title: "图表管理",
        //   path: "/charts",
        //   icon: "fa-bar-chart-o",
        //   child: [{
        //     title: "柱状图表",
        //     path: "/charts/bar"
        //   }]
        // }
        ]
      }
    }
  }
</script>
<style scoped lang="scss" type="text/scss">
    .img{
        max-width:100% !important;
        width:38px;
        height:38px;
         margin-top:16px;
        margin-bottom:49px;
    }
    .img1{
        height:38px;
          margin-top:16px;
        margin-bottom:49px;
        margin-left:-38px;
        overflow:hidden
    }
    .imgset{
        display: flex;
        align-items: center;
       justify-content: center;
        // height:30px;
        // width:88px;
    }
</style>
