
<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>课程管理</el-breadcrumb-item>
      <el-breadcrumb-item>学期列表</el-breadcrumb-item>
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
          <el-button type="primary" @click="addDialogVisible = true">添加学期</el-button>
        </el-col>
      </el-row>
      <!-- 用户列表区域 border：加表格边框-->
      <el-table :data="userlist" border stripe>
        <!-- stripe: 斑马条纹
        border：边框-->
         <!-- prop="campRef"是对应的后台返回字段 -->
         <!-- scope.row代表对应行的数据 mg_state是后台返回的bool值-->
         <!-- tooltip是鼠标放btn上对应的提示-->

        <el-table-column type="index" label="#"></el-table-column>
        <el-table-column prop="semesterNo" label="学期名称"></el-table-column>
        <el-table-column prop="startDate" label="学期开始时间"></el-table-column>
        <el-table-column prop="endDate" label="学期结束时间"></el-table-column>

        <el-table-column label="班级管理">
        <template slot-scope="scope">
            <el-button 
            type="text" 
            @click="classManage(scope.row.id)"
            plain>班级管理</el-button>
         </template>
        </el-table-column>

       
         <el-table-column label="状态" width="100">
          <template slot-scope="scope">
            <el-switch v-model="scope.row.isPublic" @change="userStateChanged(scope.row)"></el-switch>
          </template>
        </el-table-column>
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
        :total="totalCount"
      ></el-pagination>
    </el-card>

    <!-- 添加课程的对话框 -->
    <!-- 关闭的时候，调addDialogClosed -->

    <el-dialog title="添加学期" :visible.sync="addDialogVisible" width="50%" @close="addDialogClosed">
      <!-- 内容主体 -->
      <el-form
        :model="addUserForm"
        ref="addUserFormRef"
        :rules="addUserFormRules"
        label-width="100px"
      >
        <el-form-item label="学期名称" prop="semesterNo">
          <el-input v-model="addUserForm.semesterNo"></el-input>
        </el-form-item>

       <el-form-item label="开始时间" prop="startDate" width="120">
              <el-date-picker
                v-model="addUserForm.startDate"
                type="date"
                value-format="yyyy-MM-dd" 
                placeholder="选择开始日期">
              </el-date-picker>
      </el-form-item>

       <el-form-item label="结束时间" prop="endDate" width="120">
              <el-date-picker
                v-model="addUserForm.endDate"
                type="date"
                value-format="yyyy-MM-dd" 
                placeholder="选择结束日期">
              </el-date-picker>
      </el-form-item>

      <el-form-item label="是否公开" prop="isPublic">
            <el-switch v-model="addUserForm.isPublic"></el-switch>
      </el-form-item>

         <!-- <el-form-item label="用户id">
          <el-input v-model="editUserForm.userId" disabled></el-input>
        </el-form-item> -->

        <!-- <el-form-item label="学期选择" prop="termtype">
          <el-checkbox-group v-model="addUserForm.termtype">
          <el-checkbox label="学期1" name="type"></el-checkbox>
          <el-checkbox label="学期2" name="type"></el-checkbox>
          <el-checkbox label="学期3" name="type"></el-checkbox>
          </el-checkbox-group>
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
      title="修改学期信息"
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
       <el-form-item label="学期名称" prop="semesterNo">
          <el-input v-model="editUserForm.semesterNo"></el-input>
        </el-form-item>

       <el-form-item label="开始时间" prop="startDate" width="120">
              <el-date-picker
                v-model="editUserForm.startDate"
                type="date"
                value-format="yyyy-MM-dd" 
                placeholder="选择开始日期">
              </el-date-picker>
      </el-form-item>

       <el-form-item label="结束时间" prop="endDate" width="120">
              <el-date-picker
                v-model="editUserForm.endDate"
                type="date"
                value-format="yyyy-MM-dd" 
                placeholder="选择结束日期">
              </el-date-picker>
      </el-form-item>

      <el-form-item label="是否公开" prop="isPublic">
            <el-switch v-model="editUserForm.isPublic"></el-switch>
      </el-form-item>
        <!-- <el-form-item label="邮箱" prop="email">
          <el-input v-model="editUserForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="editUserForm.mobile"></el-input>
        </el-form-item> -->
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
      // 获取用户列表查询参数对象，相当于ios初始化字典
      //日期选择器
      // 获取用户列表查询参数对象，相当于ios初始化字典
      queryInfo: {
         //判断对象是否为空
        campRef:this.$route.query.courseId,
        // 当前页数
        pageNum: 1,
        // 每页显示多少数据
        pageSize: 5,
      },
      listUrl:'',
      userlist: [],
      totalCount: 0,
      isPush: false,

      // 添加课程对话框
      addDialogVisible: false,
      // 用户添加
      addUserForm: {
        campRef:this.$route.query.courseId,
        semesterNo: '',
        startDate: '',
        endDate: '',
        isPublic: 1
      },
      userId:window.sessionStorage.getItem('userId'),
      // 用户添加表单验证规则
      addUserFormRules: {
        semesterNo: [
          { required: true, message: '请输入学期名称', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '学期名称的长度在2～30个字',
            trigger: 'blur'
          }
        ],          
         startDate: [
            { type: 'string', required: true, message: '请选择开始时间', trigger: 'change' }
          ],
          endDate: [
            { type: 'string', required: true, message: '请选择结束时间', trigger: 'change' }
          ]
      },
      // 修改用户
      editDialogVisible: false,
      editUserForm: {},
      // 编辑用户表单验证
      editUserFormRules: {
       semesterNo: [
          { required: true, message: '请输入学期名称', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '学期名称的长度在2～30个字',
            trigger: 'blur'
          }
        ],          
        //  startDate: [
        //     { type: 'string', required: true, message: '请选择开始时间', trigger: 'change' }
        //   ],
        //   endDate: [
        //     { type: 'string', required: true, message: '请选择结束时间', trigger: 'change' }
        //   ]
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
    this.push =false;
     if (this.$route.query.courseId) {
            this.push = true,
            this.listUrl ='/semester'
        }
        else{
            this.push = false
            this.listUrl ='/semester/list'
        }
     this.getUserList()

        


    // if(JSON.stringify(this.$route.query.courseId) === "{}"){
    //   console.log("对象为空");
    //   this.isPush =0;
    //   } else {
    //   console.log("对象不为空");
    //   this.isPush =this.$route.query.courseId;
    //   this.getUserList()

    //   }
  },
   watch: {
        //监听相同路由下参数变化的时候，从而实现异步刷新
        '$route'(to, from) {
           this.push =false;
     if (this.$route.query.courseId) {
            this.push = true,
            this.listUrl ='/semester'
        }
        else{
            this.push = false
            this.listUrl ='/semester/list'
        }
        this.getUserList()
        },
    },
  methods: {
  //    async getAllUserList () {
  //     const { data: res } = await this.$http.get('/semester/list', {
  //       params: this.queryInfo
  //     })
  //     if (res.code !== 200) {
  //       return this.$message.error('获取用户列表失败！')
  //     }
  //     this.userlist = res.data.content
  //     this.totalCount = res.data.totalCount
  // },
    async getUserList () {

      let url =null;
         
      const { data: res } = await this.$http.get(this.listUrl, {
        params: this.queryInfo
      })
      if (res.code !== 200) {
        return this.$message.error('获取用户列表失败！')
      }
      this.userlist = res.data.content
      this.totalCount = res.data.totalCount

//       const res  = {
//          "code": 200,
//          "data":{
//      "totalCount": 1,
//     "totalPage": 1,
//     "pageSize": 1,
//     "pageNum": 2,
//     "data": [
//         {
//             "id": 3,
//             "campTitle": "title",
//             "description": "desc",
//             "isDeleted": 1,
//             "businessType": null,
//             "userId": 123,
//             "createdTime": "2020-11-03T23:44:02.000+0000",
//             "lastUpdatedTime": "2020-11-03T23:44:02.000+0000"
//         }
//     ]
//   }
   
// }
//       this.userlist = res.data.data
//       this.totalCount = res.data.totalCount

    },
    // 监听 pagesize改变的事件
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
        `users/${userInfo.id}/state/${userInfo.isDeleted}`
      )
      if (res.code !== 200) {
        userInfo.isDeleted = !userInfo.isDeleted
        return this.$message.error('更新用户状态失败')
      }
      this.$message.success('更新用户状态成功！')
    },
    // 监听 添加课程对话框的关闭事件，重置表单（去掉以前填的值）
    //refs是表单的引用，即表单实例
    addDialogClosed () {
      this.$refs.addUserFormRef.resetFields()
    },
    //班级管理
    classManage (id) {
      // this.$router.push('/terms')
      this.$router.push({
      path:'/classes',//页面路劲 和上面名字任意一个都可以
      query: {termId: id} // 参数传值
})
    },
      //章节管理
    chapterManage () {
      this.$router.push('/chapters')
    },
    // 添加课程
    addUser () {
      // 提交请求前，表单预验证
      this.$refs.addUserFormRef.validate(async valid => {
        // console.log(valid)
        // 表单预校验失败
        if (!valid) return
        const { data: res } = await this.$http.post('/semester', this.addUserForm)
        if (res.code !== 201) {
          this.$message.error('添加课程失败！')
          console.log('失败')
        }
        this.$message.success('添加课程成功！')
                  console.log('添加课程成功！')

        // 隐藏添加课程对话框
        this.addDialogVisible = false
        this.getUserList()//刷新列表
      })
    },
    // 查询一个用户信息
    async showEditDialog (id) {
      const { data: res } = await this.$http.get('semester/' + id)
      if (res.code !== 200) {
        return this.$message.error('查询学期信息失败！')
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
        const { data: res } = await this.$http.patch(
          'semester/' + this.editUserForm.id,
          {
            semesterNo: this.editUserForm.semesterNo,
            startDate: new Date(this.editUserForm.startDate),
            endDate: new Date(this.editUserForm.endDate),
            isPublic: this.editUserForm.isPublic,

          }
        )
        if (res.code !== 200) {
          this.$message.error('更新课程信息失败！')
        }
        // 隐藏添加课程对话框
        this.editDialogVisible = false
        this.$message.success('更新课程信息成功！')
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
      const { data: res } = await this.$http.delete('semester/' + id)
      if (res.code !== 200) return this.$message.error('删除课程失败！')
      this.$message.success('删除课程成功！')
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
    // // 分配角色
    // async saveRoleInfo () {
    //   if (!this.selectRoleId) {
    //     return this.$message.error('请选择要分配的角色')
    //   }
    //   const { data: res } = await this.$http.put(`users/${this.userInfo.id}/role`, { rid: this.selectRoleId })
    //   if (res.meta.status !== 200) {
    //     return this.$message.error('更新用户角色失败！')
    //   }
    //   this.$message.success('更新角色成功！')
    //   this.getUserList()
    //   this.setRoleDialogVisible = false
    // },
    // // 分配角色对话框关闭事件
    // setRoleDialogClosed () {
    //   this.selectRoleId = ''
    //   this.userInfo = {}
    // }
  }
}
</script>

<style lang="less" scoped>
</style>












