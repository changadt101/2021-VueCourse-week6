<template>
  <h1>前台產品列表</h1>

  <div class="container">
    <table class="table align-middle">
      <thead>
        <tr>
          <th>圖片</th>
          <th>商品名稱</th>
          <th>價格</th>
          <th>功能按鈕</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td style="width: 200px">
            <div
              style="height: 100px; background-size: cover; background-position: center;"
              :style="{ 'background-image': `url(${item.imageUrl})` }"
            ></div>
          </td>
          <td>{{ item.title }}</td>
          <td>
            <div class="h5" v-if="!item.price">{{ item.origin_price }} 元</div>
            <del class="h6" v-if="item.price">原價 {{ item.origin_price }} 元</del>
            <div class="h5" v-if="item.price">現在只要 {{ item.price }} 元</div>
          </td>
          <td>
            <div class="btn-group btn-group-sm">
              <button
                type="button"
                class="btn btn-outline-success"
                @click="enterProduct(item)"
              >查看產品內容</button>
              <button
                type="button"
                class="btn btn-outline-danger"
                @click="addToCart(item.id)"
                :disabled="loadingStatus.loadingItem === item.id"
              >
                <div
                  class="spinner-grow spinner-grow-sm"
                  role="status"
                  v-if="loadingStatus.loadingItem === item.id"
                >
                  <span class="visually-hidden">Loading...</span>
                </div>
                加入購物車
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import swal from 'sweetalert';

export default {
  data() {
    return {
      products: [],
      loadingStatus: {
        loadingItem: '',
      },
    };
  },
  methods: {
    enterProduct(item) {
      this.$router.push(`/product/${item.id}`);
    },
    getProducts() {
      const url = `${process.env.VUE_APP_APIURL}/${process.env.VUE_APP_APIPATH}/products`;

      this.$http.get(url)
        .then((res) => {
          if (res.data.success) {
            const { products } = res.data;

            this.products = products;
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
    addToCart(productId, quantity = 1) {
      const url = `${process.env.VUE_APP_APIURL}/${process.env.VUE_APP_APIPATH}/cart`;
      const cartParams = {
        product_id: productId,
        qty: quantity,
      };

      this.loadingStatus.loadingItem = productId;

      this.$http.post(url, { data: cartParams })
        .then((res) => {
          const { message } = res.data;
          this.loadingStatus.loadingItem = '';

          if (res.data.success) {
            swal({
              text: message,
              icon: 'success',
              button: '確定',
            });
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
    this.getProducts();
  },
};
</script>
