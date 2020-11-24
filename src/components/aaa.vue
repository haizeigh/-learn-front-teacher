<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
     <el-breadcrumb-item>课程管理</el-breadcrumb-item>
      <el-breadcrumb-item>学生列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-card>
      <!-- 搜索 添加 -->
      <!-- clearable，右侧的小x -->
      <el-row :gutter="20">
        <!-- <el-col :span="6">
          <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getUserList">
            <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col> -->
        <el-col :span="10">
          <el-button type="primary" @click="addDialogVisible = true">添加学生</el-button>
        </el-col>
      </el-row>
      <!-- 用户列表区域 border：加表格边框-->
      <el-table :data="userlist" border stripe>
        <!-- stripe: 斑马条纹
        border：边框-->
         <!-- prop="camptitle"是对应的后台返回字段 -->
         <!-- scope.row代表对应行的数据 mg_state是后台返回的bool值-->
         <!-- tooltip是鼠标放btn上对应的提示-->

        <el-table-column type="index" label="#"></el-table-column>
        <!-- <el-table-column prop="name" label="学生姓名"></el-table-column>
        <el-table-column prop="pwd" label="密码"></el-table-column>
        <el-table-column prop="userId" label="学号"></el-table-column>    -->
       
        <el-table-column label="操作">
          <template slot-scope="scope">
           
            <el-button
              type="primary"
              icon="el-icon-edit"
              size="mini"
              @click="showEditDialog(scope.row.id)"
            >编辑</el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              size="mini"
              @click="removeUserById(scope.row.id)"
            >删除</el-button>
            <!-- <el-tooltip
              class="item"
              effect="dark"
              content="角色分配"
              :enterable="false"
              placement="top"
            >
              <el-button
                type="warning"
                icon="el-icon-setting"
                size="mini"
                circle
                @click="showSetRole(scope.row)"
              ></el-button>
            </el-tooltip> -->
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页区域 -->
      <!-- page-sizes可以选择每页展示多少 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pageNum"
        :page-sizes="[2, 5, 10, 15]"
        :page-size="queryInfo.pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="totle"
      ></el-pagination>
    </el-card>

    <!-- 添加课程的对话框 -->
    <!-- 关闭的时候，调addDialogClosed -->

    <el-dialog title="添加学生" :visible.sync="addDialogVisible" width="50%" @close="addDialogClosed">
      <!-- 内容主体 -->
      <el-form
        :model="addUserForm"
        ref="addUserFormRef"
        :rules="addUserFormRules"
        label-width="100px"
      >

        <!-- <el-form-item label="学号" prop="userId">
          <el-input v-model="addUserForm.userId"
          @blur="checkBlurName(scope.row.id)"
          ></el-input>
        </el-form-item> -->

        <!-- <el-form-item label="学生姓名" prop="name">
          <el-input v-model="studentModel.name"></el-input>
        </el-form-item> -->

         <!-- <el-form-item label="密码" prop="pwd">
          <el-input v-model="addUserForm.pwd"></el-input>
        </el-form-item> -->

        


        <!-- <el-form-item label="邮箱" prop="email">
          <el-input v-model="addUserForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="addUserForm.mobile"></el-input>
        </el-form-item> -->
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addUser">确 定</el-button>
      </span>
    </el-dialog>

    <!-- 修改用户的对话框 -->
    <el-dialog
      title="修改课程信息"
      :visible.sync="editDialogVisible"
      width="50%"
      @close="editDialogClosed"
    >
      <!-- 内容主体 -->
      <!-- editDialogVisible：false即隐藏对话框 -->
      <el-form
        :model="editUserForm"
        ref="editUserFormRef"
        :rules="editUserFormRules"
        label-width="100px"
      >
        <el-form-item label="学生姓名">
          <el-input v-model="editUserForm.name" disabled></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="pwd">
          <el-input v-model="editUserForm.pwd"></el-input>
        </el-form-item>

        <el-form-item label="学号" prop="userId">
          <el-input v-model="editUserForm.userId"></el-input>
        </el-form-item>

      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editUser">确 定</el-button>
      </span>
    </el-dialog>

    <!-- 分配角色对话框
    <el-dialog title="学期设置" :visible.sync="setRoleDialogVisible" width="50%" @close="setRoleDialogClosed">
      <div>
        <p>课程：{{userInfo.camptitle}}</p>
        <p>课程简介：{{userInfo.role_name}}</p>
        <p>
          分配角色：
          <el-select
            v-model="selectRoleId"
            filterable
            allow-create
            default-first-option
            placeholder="请选择文章标签"
          >
            <el-option
              v-for="item in rolesLsit"
              :key="item.id"
              :label="item.roleName"
              :value="item.id"
            ></el-option>
          </el-select>
        </p>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="setRoleDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="saveRoleInfo">确 定</el-button>
      </span>
    </el-dialog> -->
  </div>
</template>

<script>
export default {
  data () {
    // 自定义邮箱规则
    var checkEmail = (rule, value, callback) => {
      const regEmail = /^\w+@\w+(\.\w+)+$/
      if (regEmail.test(value)) {
        // 合法邮箱
        return callback()
      }
      callback(new Error('请输入合法邮箱'))
    }
    // 自定义手机号规则
    var checkMobile = (rule, value, callback) => {
      const regMobile = /^1[34578]\d{9}$/
      if (regMobile.test(value)) {
        return callback()
      }
      // 返回一个错误提示
      callback(new Error('请输入合法的手机号码'))
    }
    return {

        value: '',
        // classId,
      // 获取用户列表查询参数对象，相当于ios初始化字典
      queryInfo: {
        // classId:this.$route.query.classId,
        classId:1,

        // // 当前页数
        pageNum: 1,
        // 每页显示多少数据
        pageSize: 5
      },
      //  queryStuNameInfo: {
      //   role:0,
      //   // 学号
      //   userId: '',
      // },
      userlist: [],
      studentModel,
      totle: 0,
      // 添加课程对话框
      addDialogVisible: false,
      // 用户添加
      addUserForm: {
        name: '',
        pwd: '',
        userId: '',
      },
      classNum: 1,
      // classId:this.$route.query.classId,
     
      // 用户添加表单验证规则
      addUserFormRules: {
        name: [
          { required: true, message: '请输入学生名称', trigger: 'blur' },
          {
            min: 2,
            max: 8,
            message: '学生姓名的长度在2～8个字',
            trigger: 'blur'
          }
        ],
        pwd: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '密码的长度在2～30个字',
            trigger: 'blur'
          }
        ],
        userId: [
          { required: true, message: '请输入学号', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '学号的长度在2～30个字',
            trigger: 'blur'
          }
        ],
      },
      // 修改用户
      editDialogVisible: false,
      editUserForm: {},
      // 编辑用户表单验证
      editUserFormRules: {
          name: [
          { required: true, message: '请输入学生名称', trigger: 'blur' },
          {
            min: 2,
            max: 8,
            message: '学生姓名的长度在2～8个字',
            trigger: 'blur'
          }
        ],
        pwd: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '密码的长度在2～30个字',
            trigger: 'blur'
          }
        ],
        userId: [
          { required: true, message: '请输入学号', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '学号的长度在2～30个字',
            trigger: 'blur'
          }
        ],
      },
      // 分配角色对话框
      setRoleDialogVisible: false,
      // 当前需要被分配角色的用户
      userInfo: {},
      // 所有角色数据列表
      rolesLsit: [],
      // 已选中的角色Id值
      selectRoleId: ''
    }
  },
  //类似于ios的viewDidLoad，下面写的是调用该方法
  created () {
    // classId:this.$route.query.classId,
    this.getUserList()
  },
  methods: {
    async getUserList () {
      const { data: res } = await this.$http.get('user/', {
        params: this.queryInfo
      })
      if (res.code !== 200) {
        return this.$message.error('获取学生列表失败！')
      }
      this.userlist = res.data.content
      this.totalCount = res.data.totalCount
    },
    // 监听 pageSize改变的事件
    handleSizeChange (newSize) {
      // console.log(newSize)
      this.queryInfo.pageSize = newSize
      //调请求方法，重新请求
      this.getUserList()
    },
    // 监听 页码值 改变事件
    handleCurrentChange (newSize) {
      // console.log(newSize)
      this.queryInfo.pageNum = newSize
      this.getUserList()
    },
    // 监听 switch开关 状态改变
    async userStateChanged (userInfo) {
      // console.log(userInfo)
      // put后台请求路径：users/:uId/state/:type
      const { data: res } = await this.$http.put(
        `users/${userInfo.id}/state/${userInfo.mg_state}`
      )
      if (res.meta.status !== 200) {
        userInfo.mg_state = !userInfo.mg_state
        return this.$message.error('更新用户状态失败')
      }
      this.$message.success('更新用户状态成功！')
    },
    // 监听 添加课程对话框的关闭事件，重置表单（去掉以前填的值）
    //refs是表单的引用，即表单实例
    addDialogClosed () {
      this.$refs.addUserFormRef.resetFields()
    },
    // //显示学生姓名
    // checkBlurName(id){
    //  const { data: res } = await this.$http.get('user/student', {
    //     params: this.queryStuNameInfo
    //   })
    //   if (res.code !== 200) {
    //     return this.$message.error('获取学生列表失败！')
    //   }
    //   this.studentModel = res.data
    //   editUserForm.name =this.studentModel.name,
    //   addUserForm.name =this.studentModel.name
    // },
    // 添加课程
    addUser () {
      // 提交请求前，表单预验证
      this.$refs.addUserFormRef.validate(async valid => {
        // console.log(valid)
        // 表单预校验失败
        if (!valid) return
//         const { data: res } = await this.$http.get('classmate/'+''window.sessionStorage.getItem('userId'''),
// +'/'+this.$route.query.classId)
        const { data: res } = await this.$http.get('classmate/'+'1'+
+'/'+'1')
        if (res.code !== 200) {
          this.$message.error('添加学生失败！')
        }
        this.$message.success('添加学生成功！')
        // 隐藏添加课程对话框
        this.addDialogVisible = false
        this.getUserList()//刷新列表
      })
    },
    // 编辑用户信息
    async showEditDialog (id) {
      const { data: res } = await this.$http.get('users/' + id)
      if (res.code !== 200) {
        return this.$message.error('查询学生信息失败！')
      }
      this.editUserForm = res.data
      this.editDialogVisible = true
    },
    // 监听修改用户对话框的关闭事件
    editDialogClosed () {
      this.$refs.editUserFormRef.resetFields()
    },
    // 修改用户信息
    editUser () {
      // 提交请求前，表单预验证
      this.$refs.editUserFormRef.validate(async valid => {
        // console.log(valid)
        // 表单预校验失败
        if (!valid) return
        const { data: res } = await this.$http.put(
          'users/' + this.editUserForm.id,
          {
            email: this.editUserForm.email,
            mobile: this.editUserForm.mobile
          }
        )
        if (res.meta.status !== 200) {
          this.$message.error('更新用户信息失败！')
        }
        // 隐藏添加课程对话框
        this.editDialogVisible = false
        this.$message.success('更新用户信息成功！')
        this.getUserList()
      })
    },
    // 删除用户
    async removeUserById (id) {
      const confirmResult = await this.$confirm(
        '此操作将永久删除该用户, 是否继续?',
        '提示',
        {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }
      ).catch(err => err)
      // 点击确定 返回值为：confirm
      // 点击取消 返回值为： cancel
      if (confirmResult !== 'confirm') {
        return this.$message.info('已取消删除')
      }
      const { data: res } = await this.$http.delete(('classmate'+self.userId+self.classId))
      if (res.meta.status !== 200) return this.$message.error('删除用户失败！')
      this.$message.success('删除用户成功！')
      this.getUserList()
    },
    // 展示分配角色的对话框
    async showSetRole (role) {
      this.userInfo = role
      // 展示对话框之前，获取所有角色列表
      const { data: res } = await this.$http.get('roles')
      if (res.meta.status !== 200) {
        return this.$message.error('获取角色列表失败！')
      }
      this.rolesLsit = res.data
      this.setRoleDialogVisible = true
    },
    // 分配角色
    async saveRoleInfo () {
      if (!this.selectRoleId) {
        return this.$message.error('请选择要分配的角色')
      }
      const { data: res } = await this.$http.put(`users/${this.userInfo.id}/role`, { rid: this.selectRoleId })
      if (res.meta.status !== 200) {
        return this.$message.error('更新用户角色失败！')
      }
      this.$message.success('更新角色成功！')
      this.getUserList()
      this.setRoleDialogVisible = false
    },
    // 分配角色对话框关闭事件
    setRoleDialogClosed () {
      this.selectRoleId = ''
      this.userInfo = {}
    },
     classNumChange (value) {
        console.log(value);
      }
  }
}
</script>

<style lang="less" scoped>
</style>
