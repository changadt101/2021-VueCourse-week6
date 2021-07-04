<template>
  <h1>後台產品列表</h1>

  <div class="container">
    <table class="table mt-4">
      <thead>
        <tr>
          <th width="120">分類</th>
          <th>產品名稱</th>
          <th width="120">原價</th>
          <th width="120">售價</th>
          <th width="100">是否啟用</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td>{{ item.category }}</td>
          <td>{{ item.title }}</td>
          <td class="text-end">{{ item.origin_price }}</td>
          <td class="text-end">{{ item.price }}</td>
          <td>
            <span v-if="item.is_enabled" class="text-success">啟用</span>
            <span v-else>未啟用</span>
          </td>
        </tr>
      </tbody>
    </table>

    <Pagination :pages="pagination" @emit-pages="getProductsListData"></Pagination>
  </div>
</template>

<script>
import swal from 'sweetalert';
import Pagination from '@/components/Pagination.vue';

export default {
  components: {
    Pagination,
  },
  data() {
    return {
      products: [],
      pagination: {},
    };
  },
  methods: {
    getProductsListData(page = 1) {
      const url = `${process.env.VUE_APP_APIURL}/${process.env.VUE_APP_APIPATH}/admin/products?page=${page}`;

      this.$http.get(url)
        .then((res) => {
          if (res.data.success) {
            const { products, pagination } = res.data;

            this.products = products;
            this.pagination = pagination;
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
  created() {
    this.getProductsListData();
  },
};
</script>
