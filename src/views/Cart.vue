<template>
  <h1>購物車頁面</h1>

  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-6">
        <table class="table align-middle">
          <thead>
            <tr>
              <th></th>
              <th>品名</th>
              <th style="width: 200px">數量/單位</th>
              <th>單價</th>
            </tr>
          </thead>

          <tbody>
            <template v-if="cartData.carts">
              <tr v-for="item in cartData.carts" :key="item.id">
                <td>
                  <button
                    type="button"
                    class="btn btn-outline-danger btn-sm"
                    @click="removeCartItem(item.id)"
                    :disabled="loadingStatus.loadingItem === item.id"
                  >
                    <div
                      class="spinner-grow spinner-grow-sm"
                      role="status"
                      v-if="loadingStatus.loadingItem === item.id"
                    >
                      <span class="visually-hidden">Loading...</span>
                    </div>
                    x
                  </button>
                </td>
                <td>
                  {{ item.product.title }}
                  <div class="text-success" v-if="item.coupon">已套用優惠券</div>
                </td>
                <td>
                  <div class="input-group input-group-sm">
                    <div class="input-group mb-3">
                      <input
                        type="number"
                        class="form-control"
                        v-model.number="item.qty"
                        @change="updateCartItem(item)"
                        :disabled="loadingStatus.loadingItem === item.id"
                        min="1"
                      >
                      <span
                        class="input-group-text"
                        id="basic-addon2"
                      >{{ item.product.unit }}</span>
                    </div>
                  </div>
                </td>
                <td class="text-end">
                  <small
                    v-if="cartData.final_total !== cartData.total"
                    class="text-success"
                  >折扣價 : </small>
                  {{ item.final_total }}
                </td>
              </tr>
            </template>
          </tbody>

          <tfoot>
            <tr>
              <td colspan="3" class="text-end">總計</td>
              <td class="text-end">{{ cartData.total }}</td>
            </tr>
            <tr v-if="cartData.final_total !== cartData.total">
              <td colspan="3" class="text-end text-success">折扣價</td>
              <td class="text-end text-success">{{ cartData.final_total }}</td>
            </tr>
          </tfoot>
        </table>
      </div>
    </div>

    <div class="my-5 row justify-content-center">
      <Form ref="cartForm" v-slot="{ errors }" @submit="submitOrder" class="col-md-6">
        <div class="mb-3">
          <label for="email" class="form-label">Email</label>
          <Field
            id="email"
            name="信箱"
            type="email"
            class="form-control"
            :class="{ 'is-invalid': errors['信箱'] }"
            placeholder="請輸入 Email"
            rules="email|required"
            v-model="formData.user.email"
          ></Field>
          <ErrorMessage name="信箱" class="invalid-feedback"></ErrorMessage>
        </div>

        <div class="mb-3">
          <label for="name" class="form-label">收件人姓名</label>
          <Field
            id="name"
            name="姓名"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors['姓名'] }"
            placeholder="請輸入姓名"
            rules="required"
            v-model="formData.user.name"
          ></Field>
          <ErrorMessage name="姓名" class="invalid-feedback"></ErrorMessage>
        </div>

        <div class="mb-3">
          <label for="telephone" class="form-label">收件人電話</label>
          <Field
            id="telephone"
            name="電話"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors['電話'] }"
            placeholder="請輸入電話"
            rules="required|min:8|max:10"
            v-model="formData.user.tel"
          ></Field>
          <ErrorMessage name="電話" class="invalid-feedback"></ErrorMessage>
        </div>

        <div class="mb-3">
          <label for="address" class="form-label">收件人地址</label>
          <Field
            id="address"
            name="地址"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors['地址'] }"
            placeholder="請輸入地址"
            rules="required"
            v-model="formData.user.address"
          ></Field>
          <ErrorMessage name="地址" class="invalid-feedback"></ErrorMessage>
        </div>

        <div class="mb-3">
          <label for="message" class="form-label">留言</label>
          <textarea
            id="message"
            name="留言"
            class="form-control"
            cols="30"
            rows="10"
            v-model="formData.message"
          ></textarea>
        </div>

        <div class="text-end">
          <button type="submit" class="btn btn-danger">送出訂單</button>
        </div>
      </Form>
    </div>
  </div>
</template>

<script>
import swal from 'sweetalert';

export default {
  data() {
    return {
      loadingStatus: {
        loadingItem: '',
      },
      formData: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: '',
        },
        message: '',
      },
      cartData: {},
    };
  },
  methods: {
    getCartData() {
      const url = `${process.env.VUE_APP_APIURL}/${process.env.VUE_APP_APIPATH}/cart`;

      this.$http.get(url)
        .then((res) => {
          if (res.data.success) {
            const { data } = res.data;

            this.cartData = data;
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
    updateCartItem(item) {
      const url = `${process.env.VUE_APP_APIURL}/${process.env.VUE_APP_APIPATH}/cart/${item.id}`;
      const cartItemParams = {
        product_id: item.product_id,
        qty: item.qty,
      };
      this.loadingStatus.loadingItem = item.id;

      this.$http.put(url, { data: cartItemParams })
        .then((res) => {
          this.loadingStatus.loadingItem = '';

          const { message } = res.data;

          if (res.data.success) {
            swal({
              text: message,
              icon: 'success',
              button: '確定',
            });

            this.getCartData();
          } else {
            swal({
              text: message,
              icon: 'error',
              button: '確定',
            });
          }
        });
    },
    removeCartItem(id) {
      const url = `${process.env.VUE_APP_APIURL}/${process.env.VUE_APP_APIPATH}/cart/${id}`;
      this.loadingStatus.loadingItem = id;

      this.$http.delete(url)
        .then((res) => {
          this.loadingStatus.loadingItem = '';

          const { message } = res.data;

          if (res.data.success) {
            swal({
              text: message,
              icon: 'success',
              button: '確定',
            });

            this.getCartData();
          } else {
            swal({
              text: message,
              icon: 'error',
              button: '確定',
            });
          }
        });
    },
    submitOrder() {
      const url = `${process.env.VUE_APP_APIURL}/${process.env.VUE_APP_APIPATH}/order`;
      const orderParams = this.formData;

      this.$http.post(url, { data: orderParams })
        .then((res) => {
          const { message } = res.data;

          if (res.data.success) {
            swal({
              text: message,
              icon: 'success',
              button: '確定',
            });

            this.$refs.cartForm.resetForm();
            this.formData.message = '';
            this.getCartData();
          } else {
            swal({
              text: message,
              icon: 'error',
              button: '確定',
            });
          }
        });
    },
  },
  created() {
    this.getCartData();
  },
};
</script>
