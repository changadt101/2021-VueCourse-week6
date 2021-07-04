<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">後台導覽列</a>
      <div class="collapse navbar-collapse" id="navbarText">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <router-link class="nav-link" to="/admin">後台首頁</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/admin/products">後台產品列表</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/admin/orders">訂單列表</router-link>
          </li>
          <li class="nav-item">
            <a href="#" class="nav-link" @click.prevent="logout">登出</a>
          </li>
        </ul>
      </div>
    </div>
  </nav>
  <router-view v-if="checkSuccess"></router-view>
</template>

<script>
import swal from 'sweetalert';

export default {
  data() {
    return {
      checkSuccess: false,
    };
  },
  methods: {
    checkLogin() {
      const token = document.cookie.replace(/(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/, '$1');

      if (token !== '') {
        this.$http.defaults.headers.common.Authorization = `${token}`;

        const url = `${process.env.VUE_APP_APIURL}/user/check`;

        this.$http.post(url)
          .then((res) => {
            if (res.data.success) {
              this.checkSuccess = true;
            } else {
              const { message } = res.data;

              swal({
                text: message,
                icon: 'error',
                button: '確定',
              });

              this.$router.push('/login');
            }
          });
      } else {
        swal({
          text: '您尚未登入。',
          icon: 'error',
          button: '確定',
        });

        this.$router.push('/login');
      }
    },
    logout() {
      document.cookie = 'hexToken=;expires=;';

      swal({
        text: 'token 已清除',
        icon: 'info',
        button: '確定',
      });

      this.$router.push('/login');
    },
  },
  created() {
    this.checkLogin();
  },
};
</script>
