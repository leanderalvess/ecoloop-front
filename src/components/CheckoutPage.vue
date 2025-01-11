<template>
  <div class="container">
    <h1>Shopping Cart 🛒</h1>
    <div v-if="cart.length > 0">
      <div v-for="item in cart" :key="item.id" class="cart-item">
        <div class="cart-details">
          <h3>{{ item.name }}</h3>
          <p>{{ item.quantity }} x ${{ item.price.toFixed(2) }}</p>
        </div>
        <button @click="removeFromCart(item)" class="btn btn-danger">Remove</button>
      </div>
      <div class="checkout-summary">
        <p class="total">Total: ${{ totalPrice.toFixed(2) }}</p>
        <div>
          <button @click="clearCart(cart)" style="margin-right: 3px;" class="btn btn-warning mr-2">Clear</button>
          <button @click="checkout" class="btn btn-success">Checkout</button>
        </div>
      </div>
    </div>
    <div v-else>
      <h5 class="empty-cart">Your cart is empty. <router-link to="/">Go back to shop</router-link>.</h5 class="">
    </div>
  </div>
</template>

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
      try {
        const apiUrl = import.meta.env.VITE_API_ENV + 'checkout';
        await axios.post(apiUrl, { cart: cart.value });
        alert('Checkout successful!');
        cart.value = [];
        localStorage.removeItem('cart');
      } catch (error) {
        console.error('error', error);
        alert('Error during checkout. Please try again.');
      }
    };

    const removeFromCart = (item: CartItem) => {
      cart.value = cart.value.filter(i => i.id !== item.id);
      localStorage.setItem('cart', JSON.stringify(cart.value));
    };

    const clearCart = (cartItens: CartItem[]) => {
      for (const item of cartItens) {
        removeFromCart(item);
      }
    };

    return { cart, totalPrice, checkout, removeFromCart, clearCart };
  },
});
</script>

<style lang="scss">
body {
  margin: 0;
  font-family: 'Poppins', sans-serif;
  background-color: #F0F0F0;
  color: #222222;
}

.container {
  padding: 20px;
  margin: auto;
  max-width: 1200px;
}

h1 {
  font-size: 2.5rem;
  text-align: center;
  margin-bottom: 2rem;
  color: #333333;
}

.empty-cart {
  font-weight: bold;
}

.cart-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: white;
  border-radius: 10px;
  padding: 15px;
  margin-bottom: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);

  .cart-details {
    flex: 1;

    h3 {
      margin: 0 0 10px;
      font-size: 1.5rem;
      font-weight: bold;
      color: #333333;
    }
  }

  .btn-danger {
    background-color: #dc3545;
    color: white;
    border: none;
    border-radius: 5px;
    padding: 10px 15px;
    cursor: pointer;

    &:hover {
      background-color: darken(#dc3545, 30%);
    }
  }
}

.checkout-summary {
  text-align: right;
  margin-top: 20px;

  .total {
    font-size: 1.5rem;
    font-weight: bold;
  }

  .btn-warning {
    border: none;
    padding: 12px 25px;
    color: white;
    border-radius: 8px;
    font-size: 1.2rem;
    cursor: pointer;

    &:hover {
      background-color: darken(#FFD700, 30%);
    }
  }

  .btn-success {
    background-color: #28a745;
    border: none;
    padding: 12px 25px;
    color: white;
    border-radius: 8px;
    font-size: 1.2rem;
    cursor: pointer;

    &:hover {
      background-color: darken(#28a745, 30%);
    }
  }
}

@media (max-width: 768px) {
  .cart-item {
    flex-direction: column;
    align-items: flex-start;
  }

  .checkout-summary {
    text-align: center;
  }
}
</style>
