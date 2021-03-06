<template>
  <table class="my-3 min-w-full table-auto shadow-lg bg-white">
    <thead class="bg-gray-100">
      <tr>
        <th class="text-left">Product</th>
        <th class="text-right">Price</th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="product in result"
        :key="product.id"
        @click="$emit('selected', product.id)"
        class="cursor-pointer hover:bg-blue-100 border-b"
      >
        <td class="text-left">{{ product.label.de || product.id }}</td>
        <td class="text-right">
          {{ product.price ? product.price.toFixed(2) : "---" }}
        </td>
      </tr>
    </tbody>
  </table>
  <div class="flex justify-between items-center">
    <div>
      <app-select :items="pageSizeOptions" v-model.number="pageSize" />
    </div>

    <div>
      <button class="border p-2" @click="prev">PREV</button>
      <button class="border p-2" @click="next">NEXT</button>
    </div>

    <div>{{ currentPage }} / {{ lastPage }}</div>
  </div>
</template>

<script>
import { toRef, watch } from "vue";
import { useRouter } from "vue-router";
import { useArrayPagination } from "../hooks/use-arraypagination";

import appSelect from "../components/Select.vue";

export default {
  emits: ["selected"],
  components: {
    appSelect,
  },
  props: {
    products: { type: Array },
  },
  setup(props) {
    const router = useRouter();
    const products = toRef(props, "products");

    const pageSizeOptions = [
      { text: "5", value: 5 },
      { text: "10", value: 10 },
      { text: "20", value: 20 },
      { text: "50", value: 50 },
      { text: "100", value: 100 },
    ];

    const {
      next,
      prev,
      currentPage,
      lastPage,
      result,
      pageSize,
      offset,
    } = useArrayPagination(products, {
      pageSize: pageSizeOptions[0].value,
      currentPage: 1,
    });

    watch(products, () => {
      currentPage.value = 1;
    });

    return {
      // pagination
      next,
      prev,
      currentPage,
      lastPage,
      pageSize,
      result,
      offset,
      // page size selector
      pageSizeOptions,
      // router
      router,
    };
  },
};
</script>

<style scoped>
td,
th {
  padding: 12px;
}
</style>