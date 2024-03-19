<template>
  <div class="login_container">
      <div class="loginBox">
        <h2>login</h2>
        <form class="login_form" :model="loginForm" :rules="loginFormRules" ref="loginFormRef">
          <div class="item" prop="userName">
            <input  v-model="loginForm.userName" placeholder="请输入用户名" clearable>
          </div>
          <div class="item">
            <input v-model="loginForm.password" type="password" placeholder="请输入密码" show-password
                   prefix-icon="iconfont icon-lock"></input>
          </div>
              <button class="btn" size="medium" :round="true" type="primary" @click="login">点击登录</button>
              <button class="btn" size="medium" :round="true" type="info" @click="resetLoginForm">恢复默认</button>
        </form>
      </div>
  </div>
</template>

<script>
export default {
  name: "Login",
  data() {
    return {
      //登录表单数据对象
      loginForm: {
        userName: 'admin',
        password: '123456'
      },
      //表单验证规则
      loginFormRules: {
        //验证用户名
        userName: [
          { required: true, message: "请输入用户名称", trigger: "blur"},
          { min:2, max: 20, message: "长度在2到20个字符之间", trigger: "blur"}
        ],
        //验证密码
        password: [
          { required: true, message: "请输入密码", trigger: "blur"},
          { min:6, max: 16, message: "长度在6到16个字符之间", trigger: "blur"}
        ]
      }
    }
  },
  methods:{
    success(params) {
      console.log(params);
      this.login()
    },
    //点击重置按钮，重置表单
    resetLoginForm(){
      console.log(this.$refs)
      this.$refs.loginFormRef.resetFields();
    },
    login() {
      this.$refs.loginFormRef.validate(async valid => {
        if(!valid) return;
        axios.defaults.headers.post['Content-Type'] = 'application/json'
        const { data: res} = await axios.post('sysUser/login', JSON.stringify(this.loginForm));
        if(res.code !== 200) return this.$message.error(res.msg);
        //控制登录权限
        if(res.data.sysUser.sysRole.children === null || res.data.sysUser.sysRole.children[0] === null) {
          this.$message.error("抱歉，您没有权限登录，请联系管理员获取权限")
          return
        }
        this.$message.success("登录成功")
        // console.log(res.data);
        //保存token
        window.sessionStorage.setItem("token", res.data.token)
        window.sessionStorage.setItem("loginUser", JSON.stringify({sysUser : res.data.sysUser, cinemaId : res.data.cinemaId, cinemaName : res.data.cinemaName}));
        // window.sessionStorage.setItem("btnPermission", res.data.sysUser.sysRole.roleId === 1 ? "admin" : "normal")
        window.sessionStorage.setItem("btnPermission", res.data.sysUser.sysRole.roleId === 1 ? "admin" : "admin")
        //导航跳转到首页
        await this.$router.push('/welcome');
      })
    }
  }
}
</script>
<style scoped>
* {
margin: 0;
padding: 0;
}

a {
text-decoration: none;
}

input,
button {
background: transparent;
border: 0;
outline: none;
}

.login_container {
  height: 100%;
background: linear-gradient(#141e30, #243b55);
display: flex;
justify-content: center;
align-items: center;
font-size: 16px;
color: #03e9f4;
}

.loginBox {
width: 400px;
height: 364px;
background-color: #0c1622;
margin: 100px auto;
border-radius: 10px;
box-shadow: 0 15px 25px 0 rgba(0, 0, 0, .6);
padding: 40px;
box-sizing: border-box;
}

h2 {
text-align: center;
color: aliceblue;
margin-bottom: 30px;
font-family: 'Courier New', Courier, monospace;
}

.item {
height: 45px;
border-bottom: 1px solid #fff;
margin-bottom: 40px;
position: relative;
}

.item input {
width: 100%;
height: 100%;
color: #fff;
padding-top: 20px;
box-sizing: border-box;
}

.item input:focus+label,
.item input:valid+label {
top: 0px;
font-size: 2px;
}

.item label {
position: absolute;
left: 0;
top: 12px;
transition: all 0.5s linear;
}

.btn {
padding: 10px 20px;
margin-top: 30px;
color: #03e9f4;
position: relative;
overflow: hidden;
text-transform: uppercase;
letter-spacing: 2px;
left: 35%;
}

.btn:hover {
border-radius: 5px;
color: #fff;
background: #03e9f4;
box-shadow: 0 0 5px 0 #03e9f4,
0 0 25px 0 #03e9f4,
0 0 50px 0 #03e9f4,
0 0 100px 0 #03e9f4;
transition: all 1s linear;
}

.btn>span {
position: absolute;
}

.btn>span:nth-child(1) {
width: 100%;
height: 2px;
background: -webkit-linear-gradient(left, transparent, #03e9f4);
left: -100%;
top: 0px;
animation: line1 1s linear infinite;
}

@keyframes line1 {

50%,
100% {
left: 100%;
}
}

.btn>span:nth-child(2) {
width: 2px;
height: 100%;
background: -webkit-linear-gradient(top, transparent, #03e9f4);
right: 0px;
top: -100%;
animation: line2 1s 0.25s linear infinite;
}

@keyframes line2 {

50%,
100% {
top: 100%;
}
}

.btn>span:nth-child(3) {
width: 100%;
height: 2px;
background: -webkit-linear-gradient(left, #03e9f4, transparent);
left: 100%;
bottom: 0px;
animation: line3 1s 0.75s linear infinite;
}

@keyframes line3 {

50%,
100% {
left: -100%;
}
}

.btn>span:nth-child(4) {
width: 2px;
height: 100%;
background: -webkit-linear-gradient(top, transparent, #03e9f4);
left: 0px;
top: 100%;
animation: line4 1s 1s linear infinite;
}

@keyframes line4 {

50%,
100% {
top: -100%;
}
}
</style>
