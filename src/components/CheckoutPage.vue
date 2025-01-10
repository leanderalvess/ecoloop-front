<script lang="ts">
import { defineComponent, ref, computed } from 'vue';
import axios from 'axios';

interface CartItem {
  id: number;
  name: string;
  price: number;
  quantity: number;
}

export default defineComponent({
  setup() {
    const cart = ref<CartItem[]>(JSON.parse(localStorage.getItem('cart') || '[]'));
    const totalPrice = computed(() => cart.value.reduce((sum, item) => sum + item.price * item.quantity, 0));

    const checkout = async () => {
      await axios.post('http://localhost:3000/checkout', { cart: cart.value });
      alert('Checkout successful!');
      cart.value = [];
      localStorage.removeItem('cart');
    };

    const removeFromCart = (item: CartItem) => {
      cart.value = cart.value.filter(i => i.id !== item.id);
      localStorage.setItem('cart', JSON.stringify(cart.value));
    };

    return { cart, totalPrice, checkout, removeFromCart };
  },
});
</script>

<template>
  <div class="container">
    <h1>Checkout</h1>
    <div v-for="item in cart" :key="item.id">
      <p>{{ item.name }} - {{ item.quantity }} x ${{ item.price.toFixed(2) }}</p>
      <button @click="removeFromCart(item)">Remove</button>
    </div>
    <p>Total: ${{ totalPrice }}</p>
    <button @click="checkout" class="btn btn-success">Proceed to Checkout</button>
  </div>
</template>
