<template>
  <div class="scanner-bar">
    <div class="input-container-wrapper">
      <form @submit.prevent="handleBarcodeSubmit" class="scanner-form">
        <div class="input-relative-block">
          <input 
            ref="inputNode"
            :value="searchQuery"
            type="text" 
            placeholder="Search product name or scan UPC..."
            class="native-input"
            @input="updateQueryValue($event.target.value)"
            @focus="showDropdown = true"
          />
          <button 
            v-if="searchQuery" 
            type="button" 
            class="clear-input-btn"
            @click="clearInputSearch"
          >
            ✕
          </button>
        </div>
      </form>

      <!-- Search Dropdown Context Overlay Menu -->
      <ul v-if="showDropdown && filteredProducts.length > 0" class="search-dropdown-menu">
        <li 
          v-for="product in filteredProducts" 
          :key="product.id"
          @click="clickDropdownSuggestion(product)"
        >
          <div class="suggest-details">
            <span class="suggest-title">{{ product.name }}</span>
            <span class="suggest-sku">UPC: {{ product.upc }}</span>
          </div>
          <span class="suggest-price">R{{ product.price.toFixed(2) }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  products: { type: Array, required: true },
  searchQuery: { type: String, required: true }
})

const emit = defineEmits(['update:searchQuery', 'scan-success', 'select-focus', 'clear-focus'])

const inputNode = ref(null)
const showDropdown = ref(false) // Declared as a reactive reference

const filteredProducts = computed(() => {
  const query = props.searchQuery.trim().toLowerCase()
  if (!query) return []
  return props.products.filter(p => p.name.toLowerCase().includes(query) || p.upc.includes(query))
})

const updateQueryValue = (value) => {
  emit('update:searchQuery', value)
  showDropdown.value = true // Corrected assignment using .value
  if (!value) {
    emit('clear-focus')
  }
}

const clearInputSearch = () => {
  emit('update:searchQuery', '')
  showDropdown.value = false // Corrected assignment using .value
  emit('clear-focus')
  if (inputNode.value) {
    inputNode.value.focus()
  }
}

const clickDropdownSuggestion = (product) => {
  emit('update:searchQuery', product.name)
  emit('select-focus', product.id)
  showDropdown.value = false // Corrected assignment using .value
}

const handleBarcodeSubmit = () => {
  const query = props.searchQuery.trim()
  if (!query) return

  const match = props.products.find(p => p.upc === query)
  if (match) {
    emit('scan-success', match)
    clearInputSearch()
  }
}
</script>

<style scoped>
.scanner-bar {
  padding: 12px 16px;
  background: var(--bg-surface);
  border-bottom: 1px solid var(--border-color);
  position: relative;
}
.input-container-wrapper {
  position: relative;
  width: 100%;
}
.input-relative-block {
  position: relative;
  width: 100%;
  display: flex;
  align-items: center;
}
.native-input {
  width: 100%;
  padding: 12px 40px 12px 16px;
  background: #f1f5f9;
  border: 2px solid transparent;
  border-radius: var(--radius-md);
  font-size: 1rem;
  outline: none;
  transition: border-color 0.15s ease, background-color 0.15s ease;
}
.native-input:focus {
  border-color: var(--brand-primary);
  background: var(--bg-surface);
}
.clear-input-btn {
  position: absolute;
  right: 12px;
  width: 22px; height: 22px; border-radius: 11px;
  background-color: #cbd5e1; color: #64748b;
  border: none; font-size: 0.75rem; font-weight: 700;
  display: flex; align-items: center; justify-content: center;
  cursor: pointer; padding: 0;
}
.clear-input-btn:hover { background-color: #94a3b8; color: var(--bg-surface); }

.search-dropdown-menu {
  position: absolute;
  top: calc(100% + 4px);
  left: 0;
  right: 0;
  background-color: var(--bg-surface);
  border-radius: var(--radius-md);
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.05);
  padding: 0;
  margin: 0;
  list-style: none;
  max-height: 260px;
  overflow-y: auto;
  z-index: 99;
  border: 1px solid var(--border-color);
}
.search-dropdown-menu li {
  padding: 12px 16px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  cursor: pointer;
  border-bottom: 1px solid var(--border-color);
  transition: background-color 0.1s ease;
}
.search-dropdown-menu li:last-child { border-bottom: none; }
.search-dropdown-menu li:hover { background-color: #f8fafc; }
.suggest-details { display: flex; flex-direction: column; gap: 2px; }
.suggest-title { font-weight: 600; font-size: 0.95rem; }
.suggest-sku { font-size: 0.8rem; color: var(--text-muted); }
.suggest-price { font-weight: 700; color: var(--brand-primary); }
</style>

