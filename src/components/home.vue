<template>
  <el-container class="home-container">
    <!-- 头部 -->
    <el-header>
      <div>
        <img src="../assets/heima.png" alt="">
        <span>电商管理系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <!-- 内容部分 -->
    <el-container>
      <!-- 侧边栏 -->
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <div class="toggle-button" @click="collapse">|||</div>
        <el-menu
        background-color="#333744"
        text-color="#fff"
        active-text-color="#409EFF"
        unique-opened
        router
        :default-active="activePath"
        :collapse="isCollapse"
        :collapse-transition='false'>
        <!-- 一级菜单 -->
          <el-submenu :index="item.id + ''" v-for="item in menuList" :key="item.id">
            <template slot="title">
              <i :class="icon[item.id]"></i>
              <span>{{item.authName}}</span>
            </template>
            <!-- 二级菜单 -->
            <el-menu-item :index="'/'+item1.path" v-for="item1 in item.children" :key="item1.id" @click="active('/'+item1.path)">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{item1.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data () {
    return {
      menuList: '',
      icon: {
        125: 'iconfont icon-user',
        103: 'iconfont icon-tijikongjian',
        101: 'iconfont icon-shangpin',
        102: 'iconfont icon-danju',
        145: 'iconfont icon-baobiao'
      },
      isCollapse: false,
      activePath: ''
    }
  },
  methods: {
    // 退出
    logout () {
      window.sessionStorage.clear('token')
      this.$router.push('/login')
    },
    // 获取菜单列表
    async getMenuList () {
      const { data: { data, meta: { status } } } = await this.$axios.get('/menus')
      if (!status === 200) return false
      else {
        this.menuList = data
        // console.log(this.menuList)
      }
    },
    // 折叠菜单
    collapse () {
      this.isCollapse = !this.isCollapse
    },
    // 菜单高亮
    active (activePath) {
      // console.log(activePath)
      this.activePath = activePath
      window.sessionStorage.setItem('activePath', activePath)
    }
  },
  created () {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  }
}
</script>

<style lang="less" scoped>
  .home-container {
    height: 100%;
    .el-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding-left: 0;
      background-color: #373D41;
      div {
        display: flex;
        align-items: center;
        span {
          color: #fff;
          font-size: 20px;
          margin-left: 10px;
          font-weight: 700;
        }
      }
    }
  }
  .el-aside {
    background-color: #333744;
    .toggle-button {
      font-size: 10px;
      color: #fff;
      text-align: center;
      line-height: 21px;
      letter-spacing: 0.2em;
      cursor: pointer;
    }
    .el-menu {
      border-right: none;
    }
  }
  .el-main {
    background-color: #eaedf1;
  }
  .iconfont {
    margin-right: 10px;
  }
</style>
