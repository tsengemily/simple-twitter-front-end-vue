<template>
  <div class="container">
    <img class="ac-logo" src="./../pic/AC_Logo.jpg" alt="" />
    <!-- https://avatars.githubusercontent.com/u/8667311?s=200&v=4 -->
    <h2 class="row justify-content-center">登入Alphitter</h2>
    <form @submit.prevent.stop="handleSubmit">
      <div class="form-group">
        <label for="account"></label>
        <input
          id="accout"
          type="text"
          class="form-control"
          placeholder="帳號"
          required
          v-model="account"
        />
      </div>
      <div class="form-group">
        <label for="password"></label>
        <input
          id="password"
          type="password"
          class="form-control"
          placeholder="密碼"
          required
          v-model="password"
          autocomplete="off"
        />
      </div>
      <button
        type="submit"
        class="btn btn-primary btn-lg btn-block"
        v-bind:disabled="isProcessing"
      >
        登入
      </button>
    </form>
    <div class="foot-container">
      <router-link class="foot-container-btn-link" to="/regist"
        >註冊Alphitter</router-link
      >
      <router-link class="foot-container-btn-link" to="/admin"
        >後台登入</router-link
      >
    </div>
  </div>
</template>

<script>
import authorizationAPI from "./../apis/authorization";
import { Toast } from "./../utils/helpers";

export default {
  name: "login",
  data() {
    return {
      account: "",
      password: "",
      isProcessing: false,
    };
  },
  methods: {
    async handleSubmit() {
      try {
        // 防止有心人士拿掉HTML的require，在JS這邊再加一道防範
        if (!this.account || !this.password) {
          Toast.fire({
            icon: "warning",
            title: "請填入 email 和 password",
          });
          return;
        }

        // 防止多次點擊送出
        this.isProcessing = true;
        const response = await authorizationAPI.signIn({
          account: this.account,
          password: this.password,
        });
        // 取得 API 請求後的資料
        const { data } = response;

        // 如果資料狀態不是success也拋出錯誤
        if (data.status !== "success") {
          throw new Error(data.message);
        }
        // 將 token和id存放在 localStorage 內
        localStorage.setItem("token", data.token);
        localStorage.setItem("userId", data.user.id);
        localStorage.setItem("avatar", data.user.avatar);
        localStorage.setItem("name", data.user.name);
        // 特過 setCurrentUser 把使用者資料存到 Vuex 的 state 中
        this.$store.commit("setCurrentUser", data.user);

        // 成功登入後轉址到餐廳首頁
        // this.$router.push("/mainpage");
        this.$router.push({ path: `/mainpage/${data.user.id}` });
      } catch (error) {
        console.log(error.response.data.message);
        // 接收錯誤
        // 將密碼欄位清空
        this.account = "";
        this.password = "";
        // 顯示錯誤提示
        Toast.fire({
          icon: "warning",
          title: error.response.data.message,
        });
        // 因為登入失敗，所以要把按鈕狀態還原
        this.isProcessing = false;
      }
    },
  },
};
</script>

<style scoped>
.ac-logo {
  margin-top: 60px;
  margin-left: calc(50% - 30px);
}
h2 {
  font-weight: 900;
  margin-top: calc(138px - 60px);
}
.btn {
  border-radius: 50px;
  border-color: rgba(255, 102, 0, 1);
  background-color: rgba(255, 102, 0, 1);
  font-weight: 900;
}
.btn:hover {
  background-color: rgb(230, 94, 4) !important;
  border-color: rgb(230, 94, 4) !important;
}
.btn:active,
.btn:focus {
  background-color: rgb(255, 145, 0) !important;
  border-color: rgb(255, 145, 0) !important;
  box-shadow: 2px 2px 2px 0px rgb(255, 145, 0) !important;
}
.router-link {
  font-weight: 900;
  font-size: 20px;
  text-align: center;
  margin-top: 5px;
  text-decoration: none;
}
.foot-container {
  display: flex;
  justify-content: flex-end;
}
.foot-container-btn-link {
  font-weight: 900;
  font-size: 20px;
  text-align: center;
  margin-top: 5px;
  text-decoration: none;
  margin-left: 50px;
}
</style>