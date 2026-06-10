<template>
  <div class="pos-app">
    <!-- Top Global Header -->
    <header class="app-header">
      <button class="menu-hamburger-btn" @click="isMenuOpen = true" aria-label="Open Menu">
        <span class="bar"></span>
        <span class="bar"></span>
        <span class="bar"></span>
      </button>

      <div class="header-title">
        <span class="title-text">{{ pageTitle }}</span>
      </div>
      
      <button 
        v-if="currentPage === 'pos'" 
        class="cart-toggle-btn" 
        :class="{ 'has-items': totalItems > 0 }" 
        @click="isCartOpen = true"
      >
        <span class="cart-icon">🛒</span>
        <span v-if="totalItems > 0" class="header-badge-count">{{ totalItems }}</span>
      </button>
      <div v-else class="header-spacer-right"></div>
    </header>

    <!-- Side Menu Drawer (Left Aligned) -->
    <AppMenu 
      :is-open="isMenuOpen" 
      :current-page="currentPage" 
      @navigate="handleNavigation"
      @close="isMenuOpen = false" 
    />

    <!-- Dynamic Router Context Container -->
    <div class="app-view-container">
      <HomeScreen 
        v-if="currentPage === 'home'" 
        @navigate="handleNavigation" 
      />
      
      <TerminalScreen 
        v-else-if="currentPage === 'pos'"
        :products="products"
        :cart="cart"
        :cart-total="cartTotal"
        :is-cart-open="isCartOpen"
        @add-to-cart="addToCart"
        @update-qty="updateQuantity"
        @remove-item="removeFromCart"
        @checkout="handleCheckout"
        @close-cart="isCartOpen = false"
      />
      
      <!-- Injected the new transaction view node screen tracking array properties below -->
      <TransactionsScreen
        v-else-if="currentPage === 'transactions'"
        :history="transactionsHistory"
      />
      
      <AboutScreen 
        v-else-if="currentPage === 'about'" 
        @navigate="handleNavigation" 
      />
    </div>

    <!-- Sticky Mobile Floating Bottom Total Panel Bar -->
    <div 
      v-if="currentPage === 'pos' && totalItems > 0" 
      class="mobile-bottom-action-bar" 
      @click="isCartOpen = true"
    >
      <div class="bar-left">
        <div class="bar-badge">{{ totalItems }}</div>
        <span class="bar-label">View Order</span>
      </div>
      <div class="bar-right">
        <span class="bar-total">R{{ cartTotal.toFixed(2) }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import AppMenu from './AppMenu.vue'
import HomeScreen from './HomeScreen.vue'
import AboutScreen from './AboutScreen.vue'
import TerminalScreen from './TerminalScreen.vue'
import TransactionsScreen from './TransactionsScreen.vue' // New view inclusion mapping

const products = ref([])
const cart = ref([])
const transactionsHistory = ref([]) // Keeps track of all processed checkouts
const currentPage = ref('home')
const isMenuOpen = ref(false)
const isCartOpen = ref(false)

const pageTitle = computed(() => {
  if (currentPage.value === 'home') return 'Welcome'
  if (currentPage.value === 'transactions') return 'Sales History'
  if (currentPage.value === 'about') return 'About System'
  return 'Order #2345'
})

const totalItems = computed(() => cart.value.reduce((sum, item) => sum + item.quantity, 0))
const cartTotal = computed(() => cart.value.reduce((sum, item) => sum + (item.price * item.quantity), 0))
/*
const fetchProducts = async () => {
  try {
    const response = await fetch('/products.json')
    products.value = await response.json()
  } catch (error) {
    console.error('Failed to load static inventory parameters payload:', error)
  }
}
*/
const fetchProducts = async () => {
  try {
    // Removed the leading forward slash to make the path relative
    const response = await fetch('products.json')
    products.value = await response.json()
  } catch (error) {
    console.error('Failed to load static inventory parameters payload:', error)
  }
}

onMounted(() => {
  fetchProducts()
})

const handleNavigation = (targetPage) => {
  currentPage.value = targetPage
  isMenuOpen.value = false
}

const addToCart = (product) => {
  const existingItem = cart.value.find(item => item.id === product.id)
  if (existingItem) {
    existingItem.quantity++
  } else {
    cart.value.push({ ...product, quantity: 1 })
  }
}

const updateQuantity = ({ id, change }) => {
  const item = cart.value.find(item => item.id === id)
  if (!item) return
  item.quantity += change
  if (item.quantity <= 0) removeFromCart(id)
}

const removeFromCart = (id) => {
  cart.value = cart.value.filter(item => item.id !== id)
}

// Rewritten payment processing handler to build historical receipt payloads
const handleCheckout = () => {
  const invoiceId = 'INV-' + Math.floor(100000 + Math.random() * 900000)
  const currentTimestamp = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })

  // Construct final historic immutable node tracking instance 
  const processedInvoice = {
    id: invoiceId,
    time: currentTimestamp,
    total: cartTotal.value,
    itemsCount: totalItems.value,
    lines: [...cart.value] // Shallow clone cart elements array list
  }

  // Prepend into history stack so newest entries show at the top of lists automatically
  transactionsHistory.value.unshift(processedInvoice)

  alert(`Payment Confirmed: Reference ${invoiceId}`)
  
  cart.value = []
  isCartOpen.value = false
  //currentPage.value = 'transactions' // Redirect route node directly into transaction list screen summary
  //currentPage.value = 'transactions';
}
</script>

<style scoped>
.pos-app { display: flex; flex-direction: column; height: 100vh; background-color: var(--bg-primary); position: relative; overflow: hidden; }
.app-header { background-color: var(--bg-dark); color: white; padding: 16px; display: flex; justify-content: space-between; align-items: center; z-index: 80; }
.menu-hamburger-btn { background: none; border: none; width: 40px; height: 40px; display: flex; flex-direction: column; justify-content: center; gap: 5px; cursor: pointer; padding: 5px; }
.menu-hamburger-btn .bar { width: 24px; height: 3px; background-color: white; border-radius: 2px; }
.title-text { font-weight: 700; font-size: 1.2rem; letter-spacing: -0.5px; }
.header-spacer-right { width: 40px; }
.cart-toggle-btn { position: relative; background: rgba(255, 255, 255, 0.1); border: none; width: 44px; height: 44px; border-radius: 22px; display: flex; align-items: center; justify-content: center; cursor: pointer; }
.cart-toggle-btn.has-items { background: rgba(59, 130, 246, 0.2); }
.cart-icon { font-size: 1.2rem; }
.header-badge-count { position: absolute; top: -4px; right: -4px; background-color: var(--brand-danger); color: white; font-size: 0.75rem; font-weight: 700; min-width: 18px; height: 18px; padding: 0 4px; border-radius: 9px; display: flex; align-items: center; justify-content: center; border: 2px solid var(--bg-dark); }
.app-view-container { flex: 1; display: flex; flex-direction: column; overflow: hidden; position: relative; }
.mobile-bottom-action-bar { position: fixed; bottom: 16px; left: 16px; right: 16px; background-color: var(--brand-primary); color: white; padding: 16px 20px; border-radius: var(--radius-md); display: flex; justify-content: space-between; align-items: center; box-shadow: 0 10px 25px -5px rgba(59, 130, 246, 0.5); cursor: pointer; z-index: 90; }
.bar-left { display: flex; align-items: center; gap: 12px; }
.bar-badge { background-color: rgba(255, 255, 255, 0.25); font-weight: 700; font-size: 0.9rem; min-width: 24px; height: 24px; border-radius: 12px; display: flex; align-items: center; justify-content: center; }
.bar-label { font-weight: 600; font-size: 1rem; }
.bar-total { font-weight: 700; font-size: 1.15rem; }

@media (max-width: 900px) {
  .app-view-container :deep(.catalog-container) { padding-bottom: 90px; }
}
@media (min-width: 901px) {
  .mobile-bottom-action-bar { display: none; }
}
</style>

