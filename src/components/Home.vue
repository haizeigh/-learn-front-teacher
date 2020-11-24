<template>
  <el-container>
    <!-- 头部 -->
    <el-header>
      <div>
        <img src="../assets/logo.png" alt />
        <span>教学系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <!-- 主体 -->
    <el-container>
      <!-- 侧边栏 -->
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <div class="toggle-button" @click="togleCollapse">|||</div>
        <el-menu unique-opened :collapse="isCollapse" :collapse-transition="false" router :default-active="activePath" background-color="#333744" text-color="#fff" active-text-color="#409FFF">
           <!-- :unique-opened="true"->只允许展开一个菜单 -->
           <!-- :collapse-transition="false" -> 关闭动画 -->
           <!-- router -> 导航开启路由模式 -->
          <!-- 一级菜单  -->
          <el-submenu :index="item.id+''" v-for="item in menuList" :key="item.id" >
            <!-- 一级菜单的模板区域 -->
            <template slot="title">
              <i :class="iconObj[item.id]"></i>
              <span>{{ item.authName}}</span>
            </template>
            <!-- 二级菜单 -->
            <el-menu-item :index="'/' + subItem.path" v-for="subItem in item.children" :key="subItem.id" @click="saveNavState('/' + subItem.path)">
              <!-- 导航开启路由模式：
                将index值作为导航路由 -->
              <!-- 二级菜单的模板区域 -->
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{ subItem.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!-- 内容主体 -->
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
      // 左侧菜单数据
      menuList: [],
      iconObj: {
        '125': 'iconfont icon-danju',
        '103': 'iconfont icon-tijikongjian',
        '101': 'iconfont icon-shangpin',
        '102': 'iconfont icon-danju',
        '145': 'iconfont icon-baobiao'
      },
      // 默认不折叠
      isCollapse: false,
      // 被激活导航地址
      activePath: ''
    }
  },
  created () {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  methods: {
    logout () {
      // 清空token
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    // 获取请求菜单
    async getMenuList () {
      // const { data: res } = await this.$http.get('menus')
      // if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      // this.menuList = res.data
      this.menuList =[{"id":125,"authName":"课程管理","path":"users","children":
      [{"id":110,"authName":"课程列表","path":"users",
      "children":[],"order":null},
           {
                "id":150,
                "authName":"学期列表",
                "path":"terms",
                "children":[

                ],
                "order":null
            },
             {
                "id":151,
                "authName":"班级列表",
                "path":"classes",
                "children":[

                ],
                "order":null
            },
            // {
            //     "id":153,
            //     "authName":"学生列表",
            //     "path":"students",
            //     "children":[

            //     ],
            //     "order":null
            // }
      ],"order":1},
      // {"id":103,"authName":"权限管理","path":"rights",
      // "children":[{"id":111,"authName":"角色列表","path":"roles",
      // "children":[],"order":null},
      // {"id":112,"authName":"权限列表","path":"rights","children":[],"order":null}],"order":2},{"id":101,"authName":"商品管理","path":"goods",
      // "children":[{"id":104,"authName":"商品列表","path":"goods",
      // "children":[],"order":1},{"id":115,"authName":"分类参数","path":"params","children":[],"order":2},
      // {"id":121,"authName":"商品分类","path":"categories","children":[],"order":3}],"order":3},{"id":102,"authName":"订单管理","path":"orders",
      // "children":[{"id":107,"authName":"订单列表","path":"orders","children":[],"order":null}],"order":4},
      {"id":145,"authName":"数据统计","path":"reports","children":[{"id":146,"authName":"数据报表","path":"reports",
      "children":[],"order":null}],"order":5}]
      // console.log("啦啦啦啦11"+JSON.stringify(res.data))
      // console.log("啦啦啦啦11"+res.data)
    },
    // 菜单的折叠与展开
    togleCollapse () {
      this.isCollapse = !this.isCollapse
    },
    // 保存连接的激活地址
    saveNavState (activePath) {
      window.sessionStorage.setItem('activePath', activePath)
    }
  }
}
</script>

<style lang="less" scoped>
.el-container {
  height: 100%;
}
.el-header {
  background-color: #373f41;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  align-items: center;
  color: #fff;
  font-size: 20px;
  > div {
    display: flex;
    align-items: center;
    img {
      height: 40px;
    }
    span {
      margin-left: 15px;
    }
  }
}
.el-aside {
  background-color: #333744;

  .el-menu {
    border: none;
  }
}
.el-main {
  background-color: #eaedf1;
}
.iconfont{
  margin-right: 10px;
}
.toggle-button {
  background-color: #4A5064;
  font-size: 10px;
  line-height: 24px;
  color: #fff;
  text-align: center;
  letter-spacing: 0.2em;
  // 鼠标放上去变成小手
  cursor: pointer;
}
</style>
