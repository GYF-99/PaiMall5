<template lang="">
  <div class="app">
    <div class="header">
      <div class="top-wrap ">
        <ul>
          <li @click="back"><img src="../../static/images/返回.png" alt=""></li>
          <li @click="forward"><img src="../../static/images/返回2.png" alt=""></li>
          <li @click="ref"><img src="../../static/images/刷新.png" alt=""></li>
        </ul>
      </div>

      <div class="header-menu">
        <router-link to="/" class="menu-link" active-class="is-active">首页</router-link>
        <router-link to="/newProducts" class="menu-link" active-class="is-active">新品推荐</router-link>
        <router-link to="/thememarket" class="menu-link" active-class="is-active">主题市场</router-link>
        <router-link to="/designerarea" class="menu-link notify" active-class="is-active">设计师专区</router-link>
      </div>
      <div class="search-bar">
        <input type="text" placeholder="搜索宝贝" @keyup.enter='toResult' v-model='searchvalue'>
      </div>
      <div class="header-profile">
        <div class="notification">
          <span class="notification-number">1</span>
          <svg viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="2" stroke-linecap="round"
            stroke-linejoin="round" class="feather feather-bell">
            <path d="M18 8A6 6 0 006 8c0 7-3 9-3 9h18s-3-2-3-9M13.73 21a2 2 0 01-3.46 0" />
          </svg>
        </div>
        <!-- <svg viewBox="0 0 512 512" fill="currentColor">
          <path
            d="M448.773 235.551A135.893 135.893 0 00451 211c0-74.443-60.557-135-135-135-47.52 0-91.567 25.313-115.766 65.537-32.666-10.59-66.182-6.049-93.794 12.979-27.612 19.013-44.092 49.116-45.425 82.031C24.716 253.788 0 290.497 0 331c0 7.031 1.703 13.887 3.006 20.537l.015.015C12.719 400.492 56.034 436 106 436h300c57.891 0 106-47.109 106-105 0-40.942-25.053-77.798-63.227-95.449z" />
        </svg> -->
        <div class="user" v-if="islogin==0">
          <img class="profile-img" :src="useravatar" alt="">
          <a href="#" class="login-link" @click="logout" style="margin-left: 22px; ">退出</a>
        </div>
        <div class="login" v-if="islogin==1">
          <a href="#" class="login-link" @click="showlogin" style="margin-left: 22px;">登录</a>
        </div>

      </div>
    </div>
    <Main></Main>
    <div class="login-box" v-if="loginstatus">
      <div class="login-header">
        <div class="login-title">登录
          <span @click="closelogin" style="float: right; margin-right: 10px; cursor: pointer;">X</span>
        </div>
      </div>
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="短信登录" name="first">
          <div class="login-content">
            <!-- 手机号 -->
            <div class="lg-mgt">
              <input v-model="userphone" type="text" placeholder="请输入手机号码" style="width: 250px;" @keyup.enter='lg_submit'>
            </div>
            <!-- 手机号 -->
            <!-- 验证码 -->
            <div class="lg-mgt">
              <input v-model="userverion" type="text" placeholder="请输入验证码" style="width: 122px;" @keyup.enter='lg_submit'>
              <a href="#" @click="lg_rverion" class="login-submit content-button status-button"
                style="font-size: 14px;">获取验证码</a>
            </div>
            <!-- 提示信息 -->
            <!-- <div class="lg-mgt">
              <span class="login-link msg" style="font-weight: bold; ">{{verionmessage}}</span>
            </div> -->
            <!-- 提示信息 -->
            <!-- 验证码 -->
            <button class="login-submit content-button status-button" @keyup.enter='lg_submit' @click="lg_submit"
              style="width: 250px;">登录</button>
          </div>

        </el-tab-pane>
        <el-tab-pane label="账号登录" name="second">
          <div class="login-content">
            <!-- 手机号 -->
            <div style="margin-top: 20px;">
              <input v-model="userphone" type="text" placeholder="请输入手机号码" style="width: 250px;" @keyup.enter='lg_submit'>
            </div>
            <!-- 手机号 -->
            <!-- 密码 -->
            <div style="margin-top: 20px;">
              <input v-model="userpwd" type="password" placeholder="请输入密码" style="width: 250px;" @keyup.enter='lg_submit'>
            </div>
            <!-- 密码 -->
            <!-- 提示信息 -->
            <!-- <div style="margin-top: 20px;">
              <span class="login-link msg" style="font-weight: bold; ">{{pwdmessage}}</span>
            </div> -->
            <!-- 提示信息 -->

            <button class="login-submit content-button status-button"  @click="lg_submit"
              style="width: 250px;">登录</button>

          </div>
        </el-tab-pane>
      </el-tabs>

      <div class="login-footer">
        <a href="#" class="login-link">
          <span>
            < </span> 其他登陆方式</a>
        <a href="#" class="login-link" style="float: right;">没有账号？免费注册</a>
      </div>
    </div>
    <div class="overlay-app"></div>
  </div>

</template>
<script>
  import { mapState } from "vuex";
  import { userlogin, getuserverion, getstatus, userlogout, getuserinfo, getuserlist, reflogin } from '../request/api.js'
  import Main from "./main.vue";
  export default {
    created() {
      this.status()
      this.usersonglist(this.$store.state.userinfo.account.id)
    },
    computed: {
      ...mapState(["userinfo"]),
      ...mapState(["useravatar"]),
      ...mapState(["islogin"]),
    },
    data() {
      return {
        //搜索框的内容
        searchvalue: "",
        loginstatus: false,
        //登录
        userphone: "",
        userpwd: "",
        userverion: "",
        // 登录信息
        pwdmessage: "",
        verionmessage: "",
        //状态
        userstatus: false,
        //标签切换
        activeName: "first",
        // 标签ID
        type: 1,

      };
    },
    //侦听器
    watch: {
      activeName() {
        this.type = 1;
        switch (this.activeName) {
          case 'first':
            this.type = 1;
            break;
          case 'second':
            this.type = 2;
            break;

          default:
            break;
        }
      },
    },
    methods: {
      //搜索框Result的内容
      toResult() {
        if (this.searchsong == "") {
          alert("内容不能为空");
        } else {
          // this.$router.push("./search?q=" + this.searchvalue);
          this.$router.push({ path: '/search', query: { keyworks: this.searchvalue } })
        }
      },
      forward() {
        this.$router.go(1);
      },
      ref() {
        this.$router.go(0);
      },
      back() {
        this.$router.back();
      },
      //标签
      handleClick(tab, event) {
        // console.log(tab, event);
      },
      // 关闭登录窗口
      closelogin() {
        this.loginstatus = false
      },
      // 打开登录窗口
      showlogin() {
        this.loginstatus = true
        // console.log(this.loginstatus);
      },
      //发送验证码
      lg_rverion() {
        getuserverion({
          phone: this.userphone,
        }).then(res => {
          console.log(res);
        })
      },
      lg_submit() {
        if (this.type == 1) {
          userlogin({
            phone: this.userphone,
            captcha: this.userverion,
          }).then(res => {
            if (res.data.code == 200) {
              this.$store.commit("adduserinfo", res.data)
              this.$store.commit("adduseravatar", res.data.profile.avatarUrl)
              this.$router.go(0);
            } else if (res.data.code == 400) {
              this.open4()
              // this.verionmessage = "手机号码或者验证码错误"
            } else if (res.data.code == 502) {
              this.open4()
              // this.pwdmessage = "手机号码或者密码错误"
            }
          })
        } else if (this.type == 2) {
          userlogin({
            phone: this.userphone,
            password: this.userpwd,
          }).then(res => {
            if (res.data.code == 200) {
              this.$store.commit("adduserinfo", res.data)
              this.$store.commit("adduseravatar", res.data.profile.avatarUrl)
              if (this.$store.state.userinfo.code = 200) {
                this.$router.go(0);
              }
            } else if (res.data.code == 400) {
              // console.log(res.data.code);
              this.open4()
              // this.pwdmessage = "手机号码或者密码错误"
            } else if (res.data.code == 502) {
              this.open4()
              // this.pwdmessage = "手机号码或者密码错误"
            }
          })
        }
      },
      open4() {
        if (this.type == 1) {
          this.$notify.error({
            title: '错误',
            message: "手机号码验证码错误"
          });
        } else if (this.type == 2) {
          this.$notify.error({
            title: '错误',
            message: "手机号码或者密码错误"
          });
        }

      },
      status() {
        getstatus().then(res => {
          let fx = res.data.data.account
          if (fx == null) {
            this.$store.commit("setislogin", 1)
          } else {
            this.$store.commit("setislogin", 0)
          }
        })
      },
      logout() {
        userlogout().then(res => {
          this.usref()
          this.$store.commit("userlogout")
          this.$store.commit("setislogin", 1)
          // this.$router.go(0);
          // this.status()
        })
      },
      usersonglist(id) {
        getuserlist({
          id
        }).then(res => {
          this.$store.commit("adduserlist", res.data.playlist)
          // console.log(res.data.playlist);
        })
      },
      usref() {
        reflogin().then(res => {
          console.log(res);
        })
      },
    },


    components: {
      Main,
    },
  };

</script>
<style lang="">
  .el-tabs__header {
    padding-left: 18px !important;
  }

  #app {
    max-width: 1500px;
    width: 100%;
  }


  .top-wrap {
    margin-right: 105px;

  }

  .top-wrap ul {
    display: flex;
    flex-direction: row;
    margin: 0;
    padding: 0;
  }

  .top-wrap ul li {
    list-style: none;
    margin: 0 10px;
    cursor: pointer;
    width: 16px;
    height: 16px;
  }

  .msg:hover {
    color: red;
  }
</style>