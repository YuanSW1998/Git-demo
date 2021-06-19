<template>
    <el-container>
        <el-header>
            <span>欢迎{{$store.state.currentUser}}使用本系统</span>
            <div><span @click="logout">退出</span></div>
        </el-header>
        <el-container>
            <el-aside width="200px">
                <el-radio-group v-model="isCollapse" style="margin-bottom: 20px;">
                    <el-radio-button :label="false">展开</el-radio-button>
                    <el-radio-button :label="true">收起</el-radio-button>
                </el-radio-group>
                <el-menu default-active="1-4-1" :router="true" class="el-menu-vertical-demo"
                         :collapse="isCollapse">
                    <el-submenu :index="item.path" v-for="item in menuList" :key="item.id">
                        <template slot="title">
                            <i class="el-icon-location"></i>
                            <span slot="title">{{item.title}}</span>
                        </template>
                        <el-menu-item :index="item2.path" v-for="item2 in item.slist" :key="item2.id">
                            <span slot="title">{{item2.title}}</span>
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
        name: "MainPage",
        data() {
            return {
                isCollapse: false,
                menuList: []

            }
        },
        methods: {
            getUname() {
                //获取session中的uname
                if (sessionStorage.getItem("user")) {
                    //修改vuex中的状态
                    this.$store.dispatch("setUser", {uname: sessionStorage.getItem("user")})
                } else {
                    this.$store.dispatch("setUser", {uname: null})

                }
            },
            logout() {
                sessionStorage.clear();
                this.$router.push("/")
            },
            //动态获取菜单
            getMenuList() {
                this.$http.get("menus").then(res => {
                    if (res.data.flag == 200) {
                        this.menuList = res.data.menus;
                    }
                })
            }
        },
        created() {
            this.getUname();
            this.getMenuList();
        }
    }
</script>

<style scoped>
    .el-header {
        background-color: #373d41;
    }

    .el-aside {
        background-color: #333744;
    }

    .el-main {
        background-color: #eaedf1;
    }

    .el-container {
        height: 100%;
    }

    /*头部样式*/
    .el-header {
        background-color: #373d41;
        display: flex;
        /*左右贴边*/
        justify-content: space-between;
        padding-left: 0;
        align-items: center;
        color: #fff;
        font-size: 20px;
    }

    .el-header > div {
        display: flex;
        align-items: center;
    }

    .el-header > div > span {
        cursor: pointer;
    }

    .el-header span {
        margin-left: 15px;

    }

    .toggleNav {
        background-color: #2F4F4F;
        color: #fff;
        font-size: 10px;
        line-height: 25px;
        letter-spacing: 0.5em;
        text-align: center;
    }
</style>