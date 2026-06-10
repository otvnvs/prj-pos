<template>
  <!-- Clicking the parent background spacing container clears the search context -->
  <div class="catalog-container" @click="handleBackgroundClick">
    <div class="card-grid">
      <div 
        v-for="product in products" 
        :key="product.id" 
        ref="itemCards"
        :data-id="product.id"
        class="touch-card"
        :class="{ 
          'is-highlighted': focusedProductId === product.id, 
          'is-dimmed': focusedProductId !== null && focusedProductId !== product.id 
        }"
        @click.stop="handleCardClick(product)"
      >
        <div v-if="getProductQuantity(product.id) > 0" class="card-count-badge">
          {{ getProductQuantity(product.id) }}
        </div>

        <div class="card-info">
          <span class="title">{{ product.name }}</span>
          <span class="sku">SKU: {{ product.upc.slice(-4) }}</span>
        </div>
        <div class="card-price">R{{ product.price.toFixed(2) }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, nextTick } from 'vue'

const props = defineProps({
  products: { type: Array, required: true },
  cart: { type: Array, required: true },
  searchQuery: { type: String, required: true },
  focusedProductId: { type: [Number, null], required: true }
})

// Added 'clear-focus' and standard model updater requirements to listeners template scope
const emit = defineEmits(['select-product', 'clear-focus', 'update:searchQuery'])

const itemCards = ref([])

const getProductQuantity = (productId) => {
  const item = props.cart.find(i => i.id === productId)
  return item ? item.quantity : 0
}

// Custom click handler logic for card interactions
const handleCardClick = (product) => {
  // Scenario 1: A search is active, and the operator clicked a grayed-out product
  if (props.focusedProductId !== null && props.focusedProductId !== product.id) {
    resetSearchContext()
    return
  }

  // Scenario 2: Normal workflow (No search active or clicked the explicitly highlighted card)
  emit('select-product', product)
}

// Handles clicking the empty container space outside of any cards
const handleBackgroundClick = () => {
  if (props.focusedProductId !== null) {
    resetSearchContext()
  }
}

// Unified state clear utility
const resetSearchContext = () => {
  emit('update:searchQuery', '')
  emit('clear-focus')
}

watch(() => props.focusedProductId, async (newId) => {
  if (newId === null) return
  await nextTick()
  const matchedElement = itemCards.value.find(el => {
    return el && Number(el.getAttribute('data-id')) === newId
  })
  if (matchedElement) {
    matchedElement.scrollIntoView({
      behavior: 'smooth',
      block: 'center'
    })
  }
})
</script>

<style scoped>
.catalog-container { flex: 1; overflow-y: auto; padding: 16px; scroll-behavior: smooth; }
.card-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 16px; }
.touch-card { position: relative; background: var(--bg-surface); border-radius: var(--radius-md); padding: 16px; min-height: 120px; display: flex; flex-direction: column; justify-content: space-between; box-shadow: 0 1px 3px rgba(0,0,0,0.05); cursor: pointer; transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1); border: 2px solid transparent; }
.touch-card:active { transform: scale(0.97); }

.touch-card.is-highlighted {
  border-color: #ff7a00;
  box-shadow: 0 0 0 4px rgba(255, 122, 0, 0.15), 0 10px 15px -3px rgba(255, 122, 0, 0.1);
  transform: translateY(-4px);
  z-index: 5;
}

.touch-card.is-dimmed {
  opacity: 0.35;
  transform: scale(0.95);
  filter: grayscale(40%);
}

.card-count-badge { position: absolute; top: -8px; right: -8px; background-color: var(--brand-primary); color: white; font-weight: 700; font-size: 0.85rem; min-width: 24px; height: 24px; padding: 0 6px; border-radius: 12px; display: flex; align-items: center; justify-content: center; box-shadow: 0 4px 6px -1px rgba(59, 130, 246, 0.4); z-index: 2; }
.title { font-weight: 600; font-size: 0.95rem; display: block; }
.sku { font-size: 0.75rem; color: var(--text-muted); margin-top: 4px; display: block; }
.card-price { font-weight: 700; color: var(--text-main); text-align: right; }
</style>

