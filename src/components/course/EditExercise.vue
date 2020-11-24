<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>题目管理</el-breadcrumb-item>
      <el-breadcrumb-item>添加题目</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-card>
      <!-- 提示 -->
      <el-alert title="添加题目信息" type="info" center show-icon :closable="false"></el-alert>
      <!-- 步骤条 -->
      <!-- <el-steps :space="200" :active="activeIndex - 0" finish-status="success" align-center>
        <el-step title="题目类型"></el-step>
        <el-step title="题目标题"></el-step>
        <el-step title="题目详情"></el-step>
        <el-step title="完成"></el-step>
      </el-steps> -->

      <el-form
        :model="addUserForm"
        ref="addUserFormRef"
        :rules="addUserFormRules"
        label-width="100px"
        >
  <el-form-item label="题目标题">
    <el-input v-model="addUserForm.stepTitle"></el-input>
  </el-form-item>
  <el-form-item label="题目类型">
    <el-radio-group v-model="addUserForm.stepType">
      <el-radio label="阅读题"></el-radio>
      <el-radio label="简单题"></el-radio>
      
    </el-radio-group>
  </el-form-item>

  <el-form-item label="题目内容" name="4">
            <!-- 富文本编辑器 -->
            <quill-editor v-model="addUserForm.content"></quill-editor>
            <!-- 添加题目 -->
            <!-- <el-button type="primary" class="btnAdd" @click="addGoods">添加题目</el-button> -->
          </el-form-item>

  <el-form-item>
    <el-button type="primary" @click="addUser">立即创建</el-button>
    <el-button>取消</el-button>
  </el-form-item>
</el-form>
</el-card>

     
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
      studentNumLimit :1,
      // 获取章节列表查询参数对象，相当于ios初始化字典
      //日期选择器
      // 获取章节列表查询参数对象，相当于ios初始化字典
      queryInfo: {
         //判断对象是否为空
        // semesterRef:this.$route.query.termId,
        // 当前页数
        pageNum: 1,
        // 每页显示多少数据
        pageSize: 5,
      },

      userlist: [],
      totalCount: 0,
      isPush: 0,

      // 添加章节对话框
      addDialogVisible: false,
      // 章节添加
      addUserForm: {
        lessonId:this.$route.query.chapterId,
        stepTitle: '',
        stepType: '',
        content:''
      },
      // 章节添加表单验证规则
      addUserFormRules: {
        stepTitle: [
          { required: true, message: '请输入章节名称', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '章节名称的长度在2～30个字',
            trigger: 'blur'
          }
        ],  
        stepType: [
          { required: true, message: '请选择题目类型', trigger: 'change' }
        ],         
      },
      // 修改章节
      editDialogVisible: false,
      editUserForm: {},
      // 编辑章节表单验证
      editUserFormRules: {
        lessonTitle: [
          { required: true, message: '请输入章节名称', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '章节名称的长度在2～30个字',
            trigger: 'blur'
          }
        ],  
        description: [
          { required: true, message: '请输入章节简介', trigger: 'blur' },
          {
            min: 6,
            max: 200,
            message: '章节简介的长度在6～200个字',
            trigger: 'blur'
          }
        ],                
        },
      // 分配角色对话框
      setRoleDialogVisible: false,
      // 当前需要被分配角色的章节
      userInfo: {},
      // 所有角色数据列表
      rolesLsit: [],
      // 已选中的角色Id值
      selectRoleId: ''
    }
  },
  //类似于ios的viewDidLoad，下面写的是调用该方法
  created () {
    
      // this.getUserList()

  },
  methods: {
    async getUserList () {
      const { data: res } = await this.$http.get('lesson/camp/'+this.$route.query.courseId, {
        params: this.queryInfo
      }) 
      if (res.code !== 200) {
        return this.$message.error('获取章节列表失败！')
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
        return this.$message.error('更新章节状态失败')
      }
      this.$message.success('更新章节状态成功！')
    },
    // 监听 添加章节对话框的关闭事件，重置表单（去掉以前填的值）
    //refs是表单的引用，即表单实例
    addDialogClosed () {
      this.$refs.addUserFormRef.resetFields()
    },
     studentLimitNumChange(value) {
        console.log(value);
      },
//       editChapter(id) {
//       // this.$router.push('/terms')
//       this.$router.push({
//       path:'/editChapters',//页面路劲 和上面名字任意一个都可以
//       query: {chapterId: id} // 参数传值
// })
//     },
    //学生管理
    exerciseManage (id) {
      // this.$router.push('/terms')
      this.$router.push({
      path:'/exercises',//页面路劲 和上面名字任意一个都可以
      query: {chapterId: id} // 参数传值
})
    },
      //学习统计
    studyStatistics () {
      this.$router.push('/chapters')
    },
    // 添加章节
    addUser () {
      // 提交请求前，表单预验证
      this.$refs.addUserFormRef.validate(async valid => {
        // console.log(valid)
        // 表单预校验失败
        if (!valid) return
        const { data: res } = await this.$http.post('lesson/step', this.addUserForm)
        if (res.code !== 200) {
          this.$message.error('添加章节失败！')
          console.log('失败')
        }
        this.$message.success('添加章节成功！')
                  console.log('添加章节成功！')

        // 隐藏添加章节对话框
        this.addDialogVisible = false
        this.getUserList()//刷新列表
      })
    },
    // 查询一个章节信息
    async showEditDialog (id) {
      const { data: res } = await this.$http.get('lesson/'+ id)
      if (res.code !== 200) {
        return this.$message.error('查询章节信息失败！')
      }
      this.editUserForm = res.data
      this.editDialogVisible = true
    },
    // 监听修改章节对话框的关闭事件
    editDialogClosed () {
      this.$refs.editUserFormRef.resetFields()
    },
    // 修改章节信息
    editUser () {
      // 提交请求前，表单预验证
      this.$refs.editUserFormRef.validate(async valid => {
        // console.log(valid)
        // 表单预校验失败
        if (!valid) return
        const { data: res } = await this.$http.put(
          'lesson/'+ this.editUserForm.id,
          {
            lessonTitle: this.editUserForm.lessonTitle,
            description: this.editUserForm.description,

          }
        )
        if (res.code !== 200) {
          this.$message.error('更新章节信息失败！')
        }
        // 隐藏添加章节对话框
        this.editDialogVisible = false
        this.$message.success('更新章节信息成功！')
        this.getUserList()
      })
    },
    // 删除章节
    async removeUserById (id) {
      const confirmResult = await this.$confirm(
        '此操作将永久删除该章节, 是否继续?',
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
      const { data: res } = await this.$http.delete('lesson/'+id)
      if (res.code !== 200) return this.$message.error('删除章节失败！')
      this.$message.success('删除章节成功！')
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
    //     return this.$message.error('更新章节角色失败！')
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
