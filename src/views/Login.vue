<template>
    <div class="login_box">
        <el-form :model="loginForm" :rules="loginRules" ref="loginForm" label-width="100px" class="demo-loginForm">
            <el-form-item label="用户名" prop="uname">
                <el-input placeholder="请输入用户名" v-model="loginForm.uname"></el-input>
            </el-form-item>
            <el-form-item label="密码" prop="pwd">
                <el-input placeholder="请输入密码" v-model="loginForm.pwd" show-password></el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="login">登录</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>
<script>
    export default {
        name: "Login",
        data() {
            return {
                //登录用户名与密码
                loginForm: {
                    uname: "",
                    pwd: "",
                },
                //表单验证规则
                loginRules: {
                    //用户名验证
                    uname: [
                        //非空验证失去焦点触发提示语言
                        {required: true, message: '请输入用户名', trigger: 'blur'},
                    ],
                    //密码验证
                    pwd: [
                        //非空验证失去焦点触发提示语言
                        {required: true, message: '请输入密码', trigger: 'blur'},

                    ],
                }

            }
        },
        methods: {
            //点击登录触发事件
            login() {
                //判断验证规则是否通过，通过返回true，否则返回false
                //loginForm对应表单的ref属性
                this.$refs["loginForm"].validate((valid) => {
                    //验证通过，将用户名与密码传递到后台判断
                    if (valid) {
                        this.$http.post("login", this.loginForm).then(res => {
                            if (res.data.flag == "ok") {
                                /*this.$message({
                                    message: '登录成功',
                                    type: 'success'
                                });*/
                                //将用户名存入session
                                sessionStorage.setItem("user",res.data.user.uname);
                                //1.提交mutation修改state中的值。
                               // this.$store.commit("setUserName", {uname: res.data.user.uname});
                                //2.分发action修改state的值
                                this.$store.dispatch("setUser",{uname: res.data.user.uname});

                                this.$router.push({path: "/mainpage"})
                            } else {
                                this.$message.error('用户名或密码错误');
                            }
                        });
                    } else {
                        return false;
                    }
                });
            }
        }
    }
</script>

<style scoped>
    .login_box {
        width: 450px;
        height: 300px;
        position: absolute;
        left: 30%;
        top: 30%;
    }
</style>