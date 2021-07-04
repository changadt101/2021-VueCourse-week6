<template>
  <h1>登入頁面</h1>

  <div class="container mt-5">
    <div class="row justify-content-center">
      <Form v-slot="{ errors }" class="col-md-6" @submit="login">
        <div class="form-floating mb-3">
          <Field
            type="email"
            class="form-control"
            id="username"
            name="使用者帳號"
            :class="{ 'is-invalid': errors['使用者帳號'] }"
            rules="email|required"
            v-model="user.username"
            placeholder="name@example.com"
            required
            autofocus
          ></Field>
          <ErrorMessage name="使用者帳號" class="invalid-feedback"></ErrorMessage>
          <label for="username">Email address</label>
        </div>

        <div class="form-floating">
          <Field
            type="password"
            class="form-control"
            id="password"
            name="密碼"
            :class="{ 'is-invalid': errors['密碼'] }"
            rules="required"
            v-model="user.password"
            placeholder="Password"
            required
          ></Field>
          <ErrorMessage name="密碼" class="invalid-feedback"></ErrorMessage>
          <label for="password">Password</label>
        </div>

        <button class="btn btn-lg btn-primary w-100 mt-3" type="submit">登入</button>
      </Form>
    </div>
  </div>
</template>

<script>
import swal from 'sweetalert';

export default {
  data() {
    return {
      user: {
        username: '',
        password: '',
      },
    };
  },
  methods: {
    login() {
      const url = `${process.env.VUE_APP_LOGINURL}`;

      this.$http.post(url, this.user)
        .then((res) => {
          if (res.data.success) {
            const { token, expired } = res.data;

            document.cookie = `hexToken=${token}; expires=${new Date(expired)}; path=/`;
            this.$router.push('/admin/products');
          } else {
            const { message } = res.data;

            swal({
              text: message,
              icon: 'error',
              button: '確定',
            });
          }
        });
    },
  },
};
</script>
