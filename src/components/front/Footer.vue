<template>
  <div class="bg-footerColor fixed-buttom">
    <footer class="container pt-3 pt-sm-5 pb-3">
      <ul class="row d-flex justify-content-around align-items-center">
        <!-- 社群 icon -->
        <li class="icon">
          <a class="btn icon__link" href="#!"
            ><i class="fab fa-facebook-f"></i
          ></a>

          <a class="btn icon__link" href="#!"><i class="fab fa-twitter"></i></a>

          <a class="btn icon__link" href="#!"><i class="fab fa-google"></i></a>

          <a class="btn icon__link" href="#!"
            ><i class="fab fa-instagram"></i
          ></a>
        </li>
        <!-- End 社群 icon -->
        <li>
          <span class="text-white d-none d-sm-block"
            >聯絡電話：04-2244-6688</span
          >
        </li>
        <li class="pt-1 pt-sm-0">
          <span class="text-white note"
            >ⓒ僅個人作品練習，無任何商業用途
            <span class="divider">
              <a
                class="icon__link pl-2"
                href="#"
                title="管理者登入"
                data-target="#loginModal"
                @click.prevent="loginModal"
                ><i class="fas fa-user"></i
              ></a>
            </span>
          </span>
        </li>
      </ul>
    </footer>

    <!-- 管理者登入 -->
    <div
      class="modal fade"
      id="loginModal"
      tabindex="-1"
      aria-labelledby="modalLabel"
      aria-hidden="true"
      data-backdrop="static"
    >
      <div class="modal-dialog">
        <div class="modal-content rounded-0">
          <div
            class="modal-header bg-mainColor modal__header--bt rounded-0 border-0"
          >
            <h4 class="modal-title text-white" id="modalLabel">管理者登入</h4>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <form @submit.prevent="signinController">
            <div class="modal-body">
              <div class="form-group">
                <label for="username" class="col-form-label">帳號</label>
                <input
                  type="text"
                  class="form-control inputDisabled"
                  id="username"
                  required
                  v-model="user.username"
                />
              </div>
              <div class="form-group">
                <label for="password" class="col-form-label">密碼</label>
                <input
                  type="password"
                  class="form-control inputDisabled"
                  id="password"
                  required
                  v-model="user.password"
                />
              </div>
            </div>
            <div
              class="modal-footer justify-content-end border-top-0"
              :class="{ 'justify-content-between': status.showMessage }"
            >
              <span class="text-danger" v-if="status.showMessage"
                >帳號或密碼錯誤，請重新登入。</span
              >
              <button type="submit" class="btn btn--baseSet btn--mainColor">
                登入
                <i
                  class="fas fa-spinner fa-spin"
                  v-if="status.buttonIsLoading"
                ></i>
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
    <!-- End 管理者登入 -->
  </div>
</template>

<script>
// 將$開頭給jquery使用
import $ from 'jquery';

export default {
  name: 'Footer',
  data() {
    return {
      // 帳號與密碼資料
      user: {
        username: '',
        password: '',
      },
      // 狀態判斷
      status: {
        // 錯誤訊息
        showMessage: false,
        // 按鈕讀取動畫
        buttonIsLoading: false,
      },
    };
  },
  methods: {
    // 登入畫面 modal
    loginModal() {
      // 轉址到後台
      this.$router.push('/admin');
    },
    // 管理者登入
    signinController() {
      const vm = this;
      // 隱藏錯誤訊息
      vm.status.showMessage = false;
      // 開啟按鈕讀取動畫
      vm.status.buttonIsLoading = true;
      // 將輸入框失效
      $('.inputDisabled').attr('disabled', true);

      const api = `${process.env.VUE_APP_APIPATH}/admin/signin`;
      vm.$http.post(api, vm.user).then((response) => {
        // 如果取得資料
        if (response.data.success) {
          // 取得api的token跟expired
          const { token } = response.data;
          const { expired } = response.data;

          // myToken 為自訂的Cookie名稱並儲存狀態; expires為到期時間
          document.cookie = `myToken=${token};expires=${new Date(expired)};`;

          // 轉址到後台
          vm.$router.push('/admin');

          // 關閉 modal
          $('#loginModal').modal('hide');
        } else {
          // 顯示錯誤訊息
          vm.status.showMessage = true;
        }
        // 關閉按鈕讀取動畫
        vm.status.buttonIsLoading = false;
        // 重啟輸入框
        $('.inputDisabled').attr('disabled', false);
      });
    },
  },
};
</script>

<style lang="scss" scoped>
@import "@/assets/styles/scss/_rwdMixin";

// icon
.icon__link {
  width: 40px;
  margin-left: 5px;
  color: #fff;
  &:focus {
    // 移除效果
    box-shadow: 0 0 0 0;
  }
  &:hover {
    color: #cacd4a;
  }
}

// 底部文字
.note {
  @include sm {
    font-size: 14px;
  }
}

// user icon 旁垂直線
.divider {
  border-left: 1px solid rgb(253, 159, 18);
  height: 30px;
  margin: 0 7.5px;
}

// 管理者登入視窗
.modal {
  // 底線
  .modal__header--bt {
    border-bottom: 2px solid #dee2e6;
  }
}
</style>
