<template>
  <section class="cart-section">
    <div class="sheet-handle" @click="$emit('close-cart')"></div>
    
    <div class="cart-header">
      <h2>Current Order</h2>
      <button class="close-icon-btn" @click="$emit('close-cart')">Close</button>
    </div>

    <div class="item-scroll-area">
      <div v-if="cart.length === 0" class="empty-state">
        No items added to current order
      </div>
      
      <div v-else v-for="item in cart" :key="item.id" class="line-item">
        <div class="details">
          <span class="item-name">{{ item.name }}</span>
          <span class="item-subtotal">R{{ (item.price * item.quantity).toFixed(2) }}</span>
        </div>
        <div class="controls">
          <div class="counter">
            <button @click="$emit('update-qty', { id: item.id, change: -1 })">-</button>
            <span class="qty">{{ item.quantity }}</span>
            <button @click="$emit('update-qty', { id: item.id, change: 1 })">+</button>
          </div>
          <button class="delete-action" @click="$emit('remove-item', item.id)">Remove</button>
        </div>
      </div>
    </div>

    <div class="cart-summary-footer">
      <div class="bill-row">
        <span>Total Due</span>
        <span class="grand-total">R{{ cartTotal.toFixed(2) }}</span>
      </div>
      <button class="primary-action-btn" :disabled="cart.length === 0" @click="$emit('checkout')">
        Proceed to Checkout
      </button>
    </div>
  </section>
</template>

<script setup>
defineProps({ cart: Array, cartTotal: Number })
defineEmits(['update-qty', 'remove-item', 'checkout', 'close-cart'])
</script>

<style scoped>
.cart-section {
  background: var(--bg-surface);
  display: flex;
  flex-direction: column;
  height: 100%;
}

.sheet-handle {
  width: 36px; height: 4px;
  background: #cbd5e1;
  border-radius: 2px;
  margin: 8px auto 0 auto;
}

.cart-header {
  padding: 16px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.cart-header h2 { margin: 0; font-size: 1.2rem; font-weight: 700; }

.close-icon-btn {
  background: none; border: none;
  color: var(--brand-primary); font-weight: 600;
}

.item-scroll-area {
  flex: 1; overflow-y: auto; padding: 0 16px;
}

.line-item {
  padding: 16px 0;
  border-bottom: 1px solid var(--border-color);
}

.details { display: flex; justify-content: space-between; margin-bottom: 12px; }
.item-name { font-weight: 500; }
.item-subtotal { font-weight: 600; }

.controls { display: flex; justify-content: space-between; align-items: center; }
.counter {
  display: flex; align-items: center;
  background: #f1f5f9; border-radius: var(--radius-sm);
}

.counter button {
  border: none; background: none;
  padding: 6px 12px; font-size: 1.1rem;
}

.qty { padding: 0 4px; font-weight: 600; }
.delete-action {
  border: none; background: none;
  color: var(--brand-danger); font-size: 0.9rem;
}

.cart-summary-footer { padding: 16px; border-top: 1px solid var(--border-color); }
.bill-row { display: flex; justify-content: space-between; margin-bottom: 16px; font-size: 1.1rem; }
.grand-total { font-weight: 700; color: var(--brand-primary); }

.primary-action-btn {
  width: 100%; padding: 16px;
  background: var(--brand-success); color: white;
  border: none; border-radius: var(--radius-md);
  font-size: 1.1rem; font-weight: 600;
}

.primary-action-btn:disabled { background: #cbd5e1; }

.empty-state { text-align: center; color: var(--text-muted); margin-top: 40px; }

@media (min-width: 901px) {
  .sheet-handle, .close-icon-btn { display: none; }
}
</style>

