<template>
  <h1>單一產品頁面 - {{ product.title }}</h1>
</template>

<script>
import swal from 'sweetalert';

export default {
  data() {
    return {
      product: {},
    };
  },
  methods: {
    getProductData() {
      const { id } = this.$route.params;

      const url = `${process.env.VUE_APP_APIURL}/${process.env.VUE_APP_APIPATH}/product/${id}`;
      this.$http.get(url)
        .then((res) => {
          if (res.data.success) {
            const { product } = res.data;

            this.product = product;
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
    this.getProductData();
  },
};
</script>
