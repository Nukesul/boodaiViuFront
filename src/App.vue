<template>
  <div id="app" class="flex flex-col min-h-screen font-sans antialiased overflow-x-hidden" :class="isDarkMode ? 'bg-gray-900 text-gray-100' : 'bg-gradient-to-br from-orange-50 to-red-100 text-gray-900'">
    <!-- Notification -->
    <transition name="fade">
      <div v-if="notification" class="fixed top-4 right-4 z-50 p-4 rounded-xl shadow-xl text-white font-medium flex items-center space-x-3" :class="notification.type === 'success' ? 'bg-gradient-to-r from-green-500 to-teal-500' : 'bg-gradient-to-r from-red-500 to-pink-500'">
        <span>{{ notification.message }}</span>
        <button @click="clearNotification" class="text-white hover:text-gray-200">
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
          </svg>
        </button>
      </div>
    </transition>

    <!-- Loading Screen -->
    <transition name="fade">
      <div v-if="isLoading" class="fixed inset-0 z-50 flex items-center justify-center bg-gradient-to-br from-orange-500 via-red-500 to-pink-500">
        <div class="text-center relative">
          <div class="w-24 h-24 border-6 border-t-6 border-white border-opacity-30 rounded-full animate-spin mb-6"></div>
          <p class="text-3xl font-extrabold text-white animate-pulse tracking-wider">Boodai Pizza</p>
          <p class="text-base text-white opacity-80 mt-2">Готовим вашу пиццу...</p>
        </div>
      </div>
    </transition>

    <!-- Header -->
    <header class="fixed w-full top-0 z-40 shadow-lg" :class="isDarkMode ? 'bg-gray-800 border-b border-gray-700' : 'bg-white border-b border-orange-200'">
      <div class="container mx-auto flex justify-between items-center py-3 px-4 sm:px-6">
        <div class="flex items-center space-x-3 cursor-pointer" @click="openMenuModal">
          <svg class="w-10 h-10 text-orange-500 animate-pulse" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v8m-4-4h8"></path>
          </svg>
          <h1 class="text-2xl font-extrabold tracking-tight bg-clip-text text-transparent bg-gradient-to-r from-orange-500 to-red-500">Boodai Pizza</h1>
        </div>
        <div class="flex items-center space-x-4 sm:space-x-6">
          <button @click="openMenuModal" class="hover:text-orange-500 transition-colors duration-300" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"></path>
            </svg>
          </button>
          <button @click="toggleCart" class="relative hover:text-orange-500 transition-colors duration-300" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"></path>
            </svg>
            <span v-if="cartCount > 0" class="absolute -top-1 -right-1 bg-gradient-to-r from-orange-500 to-red-500 text-white text-xs rounded-full h-5 w-5 flex items-center justify-center shadow-sm">{{ cartCount }}</span>
          </button>
          <button @click="toggleOrderHistory" class="hover:text-orange-500 transition-colors duration-300" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"></path>
            </svg>
          </button>
          <button @click="toggleDarkMode" class="hover:text-orange-500 transition-colors duration-300" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="isDarkMode ? 'M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z' : 'M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z'"></path>
            </svg>
          </button>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="flex-grow pt-16 sm:pt-20 container mx-auto py-10 px-4 sm:px-6">
      <h2 class="text-4xl sm:text-5xl font-extrabold mb-10 text-center tracking-tight bg-clip-text text-transparent bg-gradient-to-r from-orange-500 to-red-500">Добро пожаловать в Boodai Pizza</h2>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        <div class="bg-gradient-to-br from-orange-100 to-red-100 p-6 rounded-2xl shadow-lg" :class="isDarkMode ? 'bg-gray-800' : ''">
          <h3 class="text-2xl font-bold mb-4">О нас</h3>
          <p class="text-gray-700" :class="isDarkMode ? 'text-gray-300' : ''">Boodai Pizza - это место, где вкус встречается с традициями. Мы готовим пиццу с любовью, используя только свежие ингредиенты.</p>
        </div>
        <div class="bg-gradient-to-br from-orange-100 to-red-100 p-6 rounded-2xl shadow-lg" :class="isDarkMode ? 'bg-gray-800' : ''">
          <h3 class="text-2xl font-bold mb-4">Наше меню</h3>
          <button @click="openMenuModal" class="bg-gradient-to-r from-orange-500 to-red-500 text-white py-2 px-4 rounded-lg hover:from-orange-600 hover:to-red-600 transition-all duration-300">Посмотреть меню</button>
        </div>
      </div>
    </main>

    <!-- Menu Modal -->
    <transition name="fade">
      <div v-if="showMenuModal" class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50" @click.self="closeMenuModal">
        <div class="rounded-2xl p-6 w-full max-w-4xl shadow-2xl" :class="isDarkMode ? 'bg-gray-800' : 'bg-white'">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-2xl font-extrabold bg-clip-text text-transparent bg-gradient-to-r from-orange-500 to-red-500">Меню Boodai Pizza</h3>
            <button @click="closeMenuModal" class="hover:text-orange-500 transition-colors duration-300" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg>
            </button>
          </div>
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
            <div v-for="item in menuItems" :key="item.id" class="p-4 rounded-lg shadow-sm" :class="isDarkMode ? 'bg-gray-700' : 'bg-orange-50'">
              <img :src="item.image" :alt="item.name" class="w-full h-40 object-cover rounded-md mb-3">
              <h4 class="text-lg font-semibold" :class="isDarkMode ? 'text-gray-100' : 'text-gray-800'">{{ item.name }}</h4>
              <p class="text-sm" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">{{ item.description }}</p>
              <p class="text-orange-500 font-bold mt-2">${{ item.price }}</p>
              <button @click="addToCart(item)" class="mt-3 bg-gradient-to-r from-orange-500 to-red-500 text-white py-2 px-4 rounded-lg hover:from-orange-600 hover:to-red-600 transition-all duration-300">Добавить в корзину</button>
            </div>
          </div>
        </div>
      </div>
    </transition>

    <!-- Cart Modal -->
    <transition name="fade">
      <div v-if="showCart" class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50" @click.self="toggleCart">
        <div class="rounded-2xl p-6 w-full max-w-md shadow-2xl" :class="isDarkMode ? 'bg-gray-800' : 'bg-white'">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-2xl font-extrabold bg-clip-text text-transparent bg-gradient-to-r from-orange-500 to-red-500">Ваша корзина</h3>
            <button @click="toggleCart" class="hover:text-orange-500 transition-colors duration-300" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg>
            </button>
          </div>
          <div v-if="cart.length === 0" class="text-center py-6" :class="isDarkMode ? 'text-gray-400' : 'text-gray-600'">Корзина пуста</div>
          <div v-else>
            <ul class="space-y-3 max-h-64 overflow-y-auto mb-6">
              <li v-for="item in cart" :key="item.id" class="flex justify-between items-center p-3 rounded-lg shadow-sm" :class="isDarkMode ? 'bg-gray-700' : 'bg-orange-50'">
                <div>
                  <span class="text-sm font-semibold" :class="isDarkMode ? 'text-gray-100' : 'text-gray-800'">{{ item.name }}</span>
                  <div class="flex items-center space-x-1 mt-1">
                    <button @click="decreaseQuantity(item)" class="px-1.5 py-0.5 bg-gradient-to-r from-orange-500 to-red-500 text-white rounded">-</button>
                    <span class="text-xs" :class="isDarkMode ? 'text-gray-300' : 'text-gray-500'">{{ item.quantity }}</span>
                    <button @click="increaseQuantity(item)" class="px-1.5 py-0.5 bg-gradient-to-r from-orange-500 to-red-500 text-white rounded">+</button>
                  </div>
                </div>
                <div class="flex items-center space-x-3">
                  <span class="text-orange-500 font-bold text-sm">${{ (item.price * item.quantity).toFixed(2) }}</span>
                  <button @click="removeFromCart(item.id)" class="text-red-500 hover:text-red-700 transition-colors duration-300 text-sm">Удалить</button>
                </div>
              </li>
            </ul>
            <div class="flex justify-between items-center mb-6">
              <p class="text-lg font-semibold" :class="isDarkMode ? 'text-gray-100' : 'text-gray-800'">Итого: ${{ cartTotal }}</p>
              <button @click="clearCart" class="text-sm text-red-500 hover:text-red-700 transition-colors duration-300 font-medium">Очистить корзину</button>
            </div>
            <div class="space-y-3">
              <input v-model="order.name" type="text" class="w-full p-2.5 border rounded-lg" :class="isDarkMode ? 'bg-gray-700 border-gray-600 text-gray-100' : 'bg-white border-orange-300 text-gray-700'" placeholder="Ваше имя">
              <input v-model="order.address" type="text" class="w-full p-2.5 border rounded-lg" :class="isDarkMode ? 'bg-gray-700 border-gray-600 text-gray-100' : 'bg-white border-orange-300 text-gray-700'" placeholder="Адрес доставки">
              <select v-model="order.delivery" class="w-full p-2.5 border rounded-lg" :class="isDarkMode ? 'bg-gray-700 border-gray-600 text-gray-100' : 'bg-white border-orange-300 text-gray-700'">
                <option value="standard">Стандарт ($5)</option>
                <option value="express">Экспресс ($15)</option>
                <option value="pickup">Самовывоз</option>
              </select>
              <button @click="submitOrder" class="w-full bg-gradient-to-r from-orange-500 to-red-500 text-white py-2.5 rounded-lg hover:from-orange-600 hover:to-red-600 transition-all duration-300">Оформить заказ</button>
            </div>
          </div>
        </div>
      </div>
    </transition>

    <!-- Order History Modal -->
    <transition name="fade">
      <div v-if="showOrderHistory" class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50" @click.self="toggleOrderHistory">
        <div class="rounded-2xl p-6 w-full max-w-3xl shadow-2xl" :class="isDarkMode ? 'bg-gray-800' : 'bg-white'">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-2xl font-extrabold bg-clip-text text-transparent bg-gradient-to-r from-orange-500 to-red-500">История заказов</h3>
            <button @click="toggleOrderHistory" class="hover:text-orange-500 transition-colors duration-300" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg>
            </button>
          </div>
          <div v-if="orderHistory.length === 0" class="text-center py-6" :class="isDarkMode ? 'text-gray-400' : 'text-gray-600'">История заказов пуста</div>
          <div v-else class="space-y-4 max-h-80 overflow-y-auto">
            <div v-for="order in orderHistory" :key="order.id" class="p-4 rounded-lg shadow-sm" :class="isDarkMode ? 'bg-gray-700' : 'bg-orange-50'">
              <p class="text-sm font-semibold" :class="isDarkMode ? 'text-gray-100' : 'text-gray-800'">Заказ #{{ order.id }} от {{ formatDate(order.created_at) }}</p>
              <p class="text-xs" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">Клиент: {{ order.customer_name }}</p>
              <p class="text-xs" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">Адрес: {{ order.address }}</p>
              <p class="text-xs" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">Доставка: {{ order.delivery }}</p>
              <p class="text-xs" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">Итого: ${{ order.total }}</p>
            </div>
          </div>
        </div>
      </div>
    </transition>

    <!-- Footer -->
    <footer class="py-8 border-t" :class="isDarkMode ? 'bg-gray-800 border-gray-700' : 'bg-white border-orange-200'">
      <div class="container mx-auto text-center">
        <div class="flex items-center justify-center space-x-3 mb-4">
          <svg class="w-10 h-10 animate-pulse" :class="isDarkMode ? 'text-orange-400' : 'text-orange-500'" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v8m-4-4h8"></path>
          </svg>
          <p class="text-base font-semibold" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">© 2025 Boodai Pizza. Все права защищены.</p>
        </div>
        <div class="flex justify-center space-x-6">
          <span class="text-base font-semibold bg-clip-text text-transparent bg-gradient-to-r from-orange-500 to-red-500">Контакты: +7 (999) 123-45-67</span>
        </div>
      </div>
    </footer>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      isLoading: true,
      showCart: false,
      showMenuModal: false,
      showOrderHistory: false,
      cart: [],
      order: {
        name: '',
        address: '',
        delivery: 'standard',
      },
      orderHistory: [],
      notification: null,
      isDarkMode: false,
      menuItems: [
        { id: 1, name: 'Маргарита', description: 'Классическая пицца с томатами и моцареллой', price: 8.99, image: 'https://via.placeholder.com/300x200?text=Margherita' },
        { id: 2, name: 'Пепперони', description: 'Пикантная пицца с пепперони и сыром', price: 9.99, image: 'https://via.placeholder.com/300x200?text=Pepperoni' },
        { id: 3, name: 'Четыре сыра', description: 'Смесь четырех видов сыра', price: 10.99, image: 'https://via.placeholder.com/300x200?text=Four+Cheese' },
      ],
    };
  },
  computed: {
    cartCount() {
      return this.cart.reduce((sum, item) => sum + item.quantity, 0);
    },
    cartTotal() {
      let total = this.cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
      if (this.order.delivery === 'standard') total += 5;
      if (this.order.delivery === 'express') total += 15;
      return total.toFixed(2);
    },
  },
  mounted() {
    this.simulateLoading();
    this.loadCartFromStorage();
    this.fetchOrderHistory();
    this.loadDarkModePreference();
  },
  methods: {
    simulateLoading() {
      setTimeout(() => {
        this.isLoading = false;
      }, 2000);
    },
    async fetchOrderHistory() {
      try {
        const response = await axios.get('http://vasyaproger-backend1-c0b9.twc1.net/api/orders/');
        this.orderHistory = Array.isArray(response.data) ? response.data : [];
      } catch (error) {
        console.error('Error fetching order history:', error);
        this.orderHistory = [];
      }
    },
    toggleCart() {
      this.showCart = !this.showCart;
    },
    openMenuModal() {
      this.showMenuModal = true;
    },
    closeMenuModal() {
      this.showMenuModal = false;
    },
    toggleOrderHistory() {
      this.showOrderHistory = !this.showOrderHistory;
    },
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode;
      localStorage.setItem('darkMode', this.isDarkMode);
    },
    loadDarkModePreference() {
      const darkMode = localStorage.getItem('darkMode');
      this.isDarkMode = darkMode === 'true';
    },
    addToCart(item) {
      const existingItem = this.cart.find(cartItem => cartItem.id === item.id);
      if (existingItem) {
        existingItem.quantity += 1;
      } else {
        this.cart.push({ ...item, quantity: 1 });
      }
      this.saveCartToStorage();
      this.showNotification('success', `${item.name} добавлена в корзину!`);
    },
    removeFromCart(itemId) {
      this.cart = this.cart.filter(item => item.id !== itemId);
      this.saveCartToStorage();
      this.showNotification('success', 'Товар удалён из корзины.');
    },
    clearCart() {
      this.cart = [];
      this.saveCartToStorage();
      this.showNotification('success', 'Корзина очищена.');
    },
    increaseQuantity(item) {
      item.quantity += 1;
      this.saveCartToStorage();
    },
    decreaseQuantity(item) {
      if (item.quantity > 1) {
        item.quantity -= 1;
        this.saveCartToStorage();
      } else {
        this.removeFromCart(item.id);
      }
    },
    loadCartFromStorage() {
      const savedCart = localStorage.getItem('boodaiPizzaCart');
      if (savedCart) this.cart = JSON.parse(savedCart);
    },
    saveCartToStorage() {
      localStorage.setItem('boodaiPizzaCart', JSON.stringify(this.cart));
    },
    showNotification(type, message) {
      this.notification = { type, message };
      setTimeout(() => this.clearNotification(), 3000);
    },
    clearNotification() {
      this.notification = null;
    },
    async submitOrder() {
      if (!this.order.name || !this.order.address) {
        this.showNotification('error', 'Пожалуйста, заполните имя и адрес доставки.');
        return;
      }
      const orderData = {
        items: this.cart.map(item => ({
          id: item.id,
          name: item.name,
          price: item.price,
          quantity: item.quantity,
        })),
        total: this.cartTotal,
        customer_name: this.order.name,
        address: this.order.address,
        delivery: this.order.delivery,
        status: 'Pending',
      };
      try {
        await axios.post('http://vasyaproger-backend1-c0b9.twc1.net/api/orders/', orderData);
        this.showNotification('success', 'Заказ успешно оформлен!');
        this.cart = [];
        this.saveCartToStorage();
        this.order = { name: '', address: '', delivery: 'standard' };
        this.showCart = false;
        this.fetchOrderHistory();
      } catch (error) {
        console.error('Error submitting order:', error);
        this.showNotification('error', 'Ошибка при оформлении заказа.');
      }
    },
    formatDate(date) {
      return new Date(date).toLocaleDateString('ru-RU', { day: '2-digit', month: 'long', year: 'numeric' });
    },
  },
};
</script>

<style scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
.animate-spin {
  animation: spin 1s linear infinite;
}
@keyframes spin {
  to { transform: rotate(360deg); }
}
.animate-pulse {
  animation: pulse 1.5s infinite;
}
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.7; }
}
button {
  transition: all 0.3s ease;
}
button:hover:not(:disabled) {
  transform: scale(1.03);
}
button:active:not(:disabled) {
  transform: scale(0.97);
}

@media (max-width: 640px) {
  .container {
    padding-left: 1rem;
    padding-right: 1rem;
  }
  .pt-16 {
    padding-top: 4.5rem;
  }
  .text-4xl {
    font-size: 2rem;
  }
}
</style>