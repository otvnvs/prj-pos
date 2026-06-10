<template>
  <div class="terminal-view-layout">
    <ScannerBar 
      v-model:search-query="searchQuery"
      :products="products" 
      @scan-success="$emit('add-to-cart', $event)" 
      @select-focus="handleProductFocus"
      @clear-focus="handleClearFocus"
    />

    <main class="app-body">
      <ProductCatalog 
        v-model:search-query="searchQuery"
        :products="products" 
        :cart="cart"
        :focused-product-id="focusedProductId"
        @select-product="$emit('add-to-cart', $event)" 
        @clear-focus="handleClearFocus"
      />
      
      <div class="cart-responsive-wrapper" :class="{ 'mobile-open': isCartOpen }">
        <div class="cart-mobile-backdrop" @click="$emit('close-cart')"></div>
        <CartSection 
          :cart="cart" 
          :cart-total="cartTotal" 
          @update-qty="$emit('update-qty', $event)" 
          @remove-item="$emit('remove-item', $event)" 
          @checkout="executeTerminalCheckout"
          @close-cart="$emit('close-cart')"
        />
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ScannerBar from './ScannerBar.vue'
import ProductCatalog from './ProductCatalog.vue'
import CartSection from './CartSection.vue'

defineProps({
  products: { type: Array, required: true },
  cart: { type: Array, required: true },
  cartTotal: { type: Number, required: true },
  isCartOpen: { type: Boolean, required: true }
})

const emit = defineEmits(['add-to-cart', 'update-qty', 'remove-item', 'checkout', 'close-cart'])

const searchQuery = ref('')
const focusedProductId = ref(null)

const handleProductFocus = (productId) => {
  focusedProductId.value = productId
}

const handleClearFocus = () => {
  focusedProductId.value = null
}

// Unified interceptor tracking rules
const executeTerminalCheckout = () => {
  // Clear local component states to ensure fresh reload context later
  searchQuery.value = ''
  focusedProductId.value = null
  
  // Close the open tray view on mobile screens
  emit('close-cart')
  
  // Bubble event execution layer up to the root orchestrator module
  emit('checkout')
}
</script>

<style scoped>
.terminal-view-layout { flex: 1; display: flex; flex-direction: column; overflow: hidden; }
.app-body { display: flex; flex: 1; overflow: hidden; position: relative; }

@media (max-width: 900px) {
  .cart-responsive-wrapper { position: fixed; top: 0; left: 0; right: 0; bottom: 0; z-index: 100; visibility: hidden; transition: visibility 0.3s; }
  .cart-responsive-wrapper.mobile-open { visibility: visible; }
  .cart-mobile-backdrop { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: rgba(15, 23, 42, 0.6); opacity: 0; transition: opacity 0.3s; }
  .cart-responsive-wrapper.mobile-open .cart-mobile-backdrop { opacity: 1; }
  .cart-responsive-wrapper :deep(.cart-section) { position: absolute; bottom: 0; left: 0; right: 0; height: 85vh; border-radius: var(--radius-lg) var(--radius-lg) 0 0; transform: translateY(100%); transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
  .cart-responsive-wrapper.mobile-open :deep(.cart-section) { transform: translateY(0); }
}
@media (min-width: 901px) {
  .cart-responsive-wrapper { width: 380px; display: flex; flex-direction: column; border-left: 1px solid var(--border-color); }
  .cart-mobile-backdrop { display: none; }
}
</style>

