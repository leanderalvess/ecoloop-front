<template>
  <div class="container">
    <h1>Welcome to Ecoloop's store</h1>
    <div class="filters">
      <input v-model="filters.name" type="string" placeholder="Search product" class="input-field" />
      <input v-model.number="filters.minPrice" type="number" placeholder="Min Price" class="input-field" />
      <input v-model.number="filters.maxPrice" type="number" placeholder="Max Price" class="input-field" />
      <button @click="fetchProducts()" class="btn btn-secundary">🔎</button>
    </div>
    <div class="product-grid">
      <div v-for="product in products" :key="product.id" class="product-card">
        <img :src="findImage(product.name)" alt="Product Image" class="product-image" />
        <div class="product-info">
          <h5>{{ product.name }}</h5>
          <p>${{ product.price.toFixed(2) }}</p>
          <button @click="addToCart(product)" class="btn btn-add" :class="{ 'added-animation': product.isAdded }">
            Add to Cart
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import axios from 'axios';

interface Product {
  id: number;
  name: string;
  price: number;
  image: string;
  quantity: number;
  isAdded?: boolean;
}

const findImage = (name: string): string => {
  try {
    const images = {
      'jeans': new URL('@/assets/jeans.jpg', import.meta.url).href,
      't-shirt': new URL('@/assets/tshirt.jpg', import.meta.url).href,
    };
    return images[name.toLowerCase()] || new URL('@/assets/default.jpg', import.meta.url).href;
  } catch {
    return '';
  }
};


export default defineComponent({
  setup() {
    const products = ref<Product[]>([]);
    const filters = ref<{ name: string; minPrice: number | null; maxPrice: number | null }>({
      name: '',
      minPrice: null,
      maxPrice: null,
    });
    const cart = ref<Product[]>(JSON.parse(localStorage.getItem('cart') || '[]'));

    const fetchProducts = async () => {
      const apiUrl = import.meta.env.VITE_API_ENV + 'products';
      const response = await axios.get(apiUrl, { params: filters.value });
      products.value = response.data;
    };

    const addToCart = (product: Product) => {
      const existing = cart.value.find(item => item.id === product.id);
      if (existing) {
        existing.quantity++;
      } else {
        cart.value.push({ ...product, quantity: 1 });
      }
      localStorage.setItem('cart', JSON.stringify(cart.value));
      product.isAdded = true;
      setTimeout(() => (product.isAdded = false), 500);
    };


    fetchProducts();

    return { products, filters, cart, addToCart, fetchProducts, findImage };
  },
});
</script>

<style lang="scss">
.filters {
  display: flex;
  gap: 15px;
  margin-bottom: 30px;
  justify-content: center;
  flex-wrap: wrap;

  .input-field {
    flex: 1;
    padding: 15px;
    border: 1px solid #555555;
    border-radius: 8px;
    font-size: 1.1rem;
    min-width: 250px;
    margin: 5px 0;
  }

  .btn-primary {
    background-color: #ffeaa7;
    color: white;
    border: none;
    padding: 15px 30px;
    font-size: 1.2rem;
    border-radius: 8px;

    &:hover {
      background-color: darken(#ffeaa7, 30%);
    }
  }
}

.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 25px;
}

.product-card {
  background-color: white;
  border-radius: 10px;
  overflow: hidden;
  text-align: center;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
  transition: transform 0.3s ease;

  &:hover {
    transform: scale(1.05);
  }

  .product-image {
    width: 100%;
    height: 250px;
    object-fit: cover;
    border-radius: 8px;
  }

  .product-info {
    padding: 20px;

    h5 {
      font-size: 1.5rem;
      margin-bottom: 15px;
      color: #333333;
    }

    .btn-add {
      background-color: #00b894;
      color: white;
      padding: 12px 25px;
      border-radius: 8px;
      border: none;
      font-size: 1rem;
      cursor: pointer;

      &:hover {
        background-color: darken(#00b894, 30%);
      }

      &.added-animation {
        animation: bounce 0.3s ease-in-out;
      }
    }
  }
}

@keyframes bounce {
  0% {
    transform: scale(1);
  }

  50% {
    transform: scale(1.1);
  }

  100% {
    transform: scale(1);
  }
}

@media (max-width: 768px) {
  .filters {
    flex-direction: column;
  }

  .input-field {
    width: 100%;
  }
}
</style>
