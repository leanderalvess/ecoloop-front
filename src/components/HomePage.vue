<script lang="ts">
import { defineComponent, ref } from 'vue';
import axios from 'axios';

interface Product {
  id: number;
  name: string;
  price: number;
  image: string;
  quantity: number;
}

export default defineComponent({
  setup() {
    const products = ref<Product[]>([]);
    const filters = ref<{ name: string; minPrice: number | null; maxPrice: number | null }>({
      name: '',
      minPrice: null,
      maxPrice: null,
    });
    const cart = ref<Product[]>([]);

    const fetchProducts = async () => {
      const response = await axios.get('http://localhost:3000/products', { params: filters.value });
      products.value = response.data;
    };

    const addToCart = (product: Product) => {
      const existing = cart.value.find(item => item.id === product.id);
      if (existing) {
        existing.quantity++;
      } else {
        cart.value.push({ ...product, quantity: 1 });
      }
    };

    fetchProducts();

    return { products, filters, cart, addToCart };
  },
});
</script>

<template>
  <div class="container">
    <h1>Store</h1>
    <div>
      <input v-model="filters.name" placeholder="Search by name" />
      <input v-model.number="filters.minPrice" type="number" placeholder="Min Price" />
      <input v-model.number="filters.maxPrice" type="number" placeholder="Max Price" />
    </div>
    <div class="row">
      <div v-for="product in filteredProducts" :key="product.id" class="col-4">
        <div class="card">
          <img :src="product.image" class="card-img-top" />
          <div class="card-body">
            <h5 class="card-title">{{ product.name }}</h5>
            <p class="card-text">${{ product.price.toFixed(2) }}</p>
            <button @click="addToCart(product)" class="btn btn-primary">Add to Cart</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
