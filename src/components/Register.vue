<template>
  <div class="login_container">
    <div class="login_box">
      <!-- 头像区 -->
      <div class="avatar_box">
        <img src="../assets/imags/login_teacher.png" alt="avatar" />
      </div>
      <!-- 登录表单 element固定的表单格式，其网站上可以找到源代码-->
            <!-- 需要在plugins下的element中导入element-ui中用到的组件，比如：el-input-->

      <div>
        <!-- el表单绑定数据用:model,绑定数据对象loginForm上（data中）-->
        <!-- v-model="loginForm.name"，绑定到对象的一个属性上-->

        <!-- :rules="loginFormRules"是表单的验证规则，data中定义-->
            <!-- prop="name"对应规则中的具体-->
        <!--ref="loginFormRef"是表单的整个对象-，methods中的this就是指改引用对象，就是表单的实例-->
        <el-form
          ref="loginFormRef"
          :model="loginForm"
          :rules="loginFormRules"
          label-width="60px"
          class="login_form"
        >
         <!-- prefix-icon是el表单对应的属性，文档中有，可以用el官网中自带的图标，也可以引入阿里矢量图标库，类iconfont.css中就是图标库-->
        <!-- 类iconfont.css引入方法：文件demo_fontclass.html在浏览器中打开（右击选择copy path 或者直接拉入到浏览器中），里面有使用步骤-->
                        <!-- 在main中导入字体图标，全局的一般都放在main中-->
                        <!-- iconfont是固定的，icon-3702mima是图片的名称-->
                        <!-- type="pwd"，表单为密码形式-->
          <el-form-item label="账号" prop="name">
            <el-input v-model="loginForm.name" prefix-icon="iconfont icon-user"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="pwd">
            <el-input
              v-model="loginForm.pwd"
              type="pwd"
              prefix-icon="iconfont icon-3702mima"
            ></el-input>
          </el-form-item>
          <el-form-item class="btns">
                        <!-- 加btns这个class为了整体的定位-->
            <el-button type="primary" @click="login">注册</el-button>
            <el-button type="info" @click="resetLoginForm">重置</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loginForm: {
        name: 'admin',
        pwd: '123456',
        role: '1'

      },
      loginFormRules: {
        name: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 2, max: 10, message: '长度在 2 到 10 个字符', trigger: 'blur' }
        ],
        pwd: [
          { required: true, message: '请输入用户密码', trigger: 'blur' },
          { min: 6, max: 18, message: '长度在 6 到 18 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    // 表单重置按钮，重制后显示表单的默认值
    resetLoginForm () {
      // console.log(this)
      // resetFields：element-ui提供的表单方法
      this.$refs.loginFormRef.resetFields()
    },
    login () {
      // window.sessionStorage.setItem('userId', '123456')
      // this.$router.push('/home')

      //表单预验证
      //valid：bool类型
      //loginFormRef是表单的引用
      //valid是一个验证后的bool值的属性，只有一个所以（）省略了
      //在main中导入axios的包，然后设置全局引用
      //main中设置请求跟路径
      //用户所填写的数据，都会自动同步到this.loginForm这个对象中
      //服务器返回的很多，我们只要data下的，重命名为res
      //用await修饰，可以直接拿到请求的数据，但是用await的的方法必须用async
      this.$refs.loginFormRef.validate(async valid => {
        // console.log(valid)
        if (!valid) return false
        // this.$http.post('login', this.loginForm): 返回值为promise
        // 返回值为promise，可加await简化操作 相应的也要加async
        const { data: res } = await this.$http.post('user/register', this.loginForm)
        console.log(res)
        //先在element.js导入message组件
        if (res.code !== 200) return this.$message.error(res.message)
        this.$message.success('注册成功')
        console.log('注册成功')

        // 1、将登陆成功之后的token, 保存到客户端的sessionStorage（会话期间的存储）中; localStorage中是持久化的保存
        //   1.1 项目中出现了登录之外的其他API接口，必须在登陆之后才能访问
        //   1.2 token 只应在当前网站打开期间生效，所以将token保存在sessionStorage中
        // 2、通过编程式导航跳转到后台主页, 路由地址为：/home
        this.$router.push('/login')
      })
    }
  }
}
</script>

<style lang="less" scoped>
/* // lang="less" 支持less格式
// scoped vue的指令，只在当前组件生效 */
.login_container {
  background-color: #2b4b6b;
  height: 100%;
}
.login_box {
  width: 450px;
  height: 360px;
  background-color: #fff;
  border-radius: 3px;
  position: absolute;//绝对定位
  left: 50%;//距离左侧偏移50%，先放到屏幕上一个固定的位置，然后再横纵轴偏移
  top: 50%;
  -webkit-transform: translate(-50%, -50%);//横轴上移动50%。这样就把盒子放到正中间
  background-color: #fff;

  .avatar_box {
    width: 130px;
    height: 130px;
    border: 1px solid #eee;//边框
    border-radius: 50%;
    padding: 10px;
    box-shadow: 0 0 10px #ddd;//阴影
    position: absolute;//
    left: 50%;//父的50%
    transform: translate(-50%, -50%);//移动自身的50%
    background-color: #fff;
    img {
      width: 100%;//对于父盒子
      height: 100%;
      border-radius: 50%;//圆角
      background-color: #eee;
    }
  }
}
.login_form {
  position: absolute;
  bottom: 60px;
  width: 100%;
  padding: 0 20px;
  box-sizing: border-box;//设置表单样式
}
.btns {
  display: flex;
  justify-content: center;
}
.info {
  font-size: 13px;
  margin: 10px 15px;
}
</style>
