<template>
  <div class="menu-drawer-wrapper" :class="{ 'drawer-open': isOpen }">
    <div class="drawer-backdrop" @click="$emit('close')"></div>
    <nav class="drawer-panel">
      <div class="drawer-header">
        <h3>Navigation</h3>
        <button class="drawer-close-btn" @click="$emit('close')">✕</button>
      </div>
      <ul class="drawer-links-list">
        <li :class="{ 'active-link': currentPage === 'home' }" @click="$emit('navigate', 'home')">
          <span class="link-icon"></span> Home
        </li>
        <li :class="{ 'active-link': currentPage === 'pos' }" @click="$emit('navigate', 'pos')">
          <span class="link-icon"></span> POS Terminal
        </li>
        <!-- Injected menu router trigger node item link block -->
        <li :class="{ 'active-link': currentPage === 'transactions' }" @click="$emit('navigate', 'transactions')">
          <span class="link-icon"></span> Transactions
        </li>
        <li :class="{ 'active-link': currentPage === 'about' }" @click="$emit('navigate', 'about')">
          <span class="link-icon"></span> About
        </li>
      </ul>
    </nav>
  </div>
</template>

<script setup>
defineProps({
  isOpen: { type: Boolean, required: true },
  currentPage: { type: String, required: true }
})
defineEmits(['close', 'navigate'])
</script>

<style scoped>
.menu-drawer-wrapper { position: fixed; top: 0; left: 0; right: 0; bottom: 0; z-index: 200; visibility: hidden; transition: visibility 0.3s; }
.menu-drawer-wrapper.drawer-open { visibility: visible; }
.drawer-backdrop { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: rgba(15, 23, 42, 0.5); opacity: 0; transition: opacity 0.3s ease; }
.menu-drawer-wrapper.drawer-open .drawer-backdrop { opacity: 1; }
.drawer-panel { position: absolute; top: 0; left: 0; bottom: 0; width: 280px; background-color: var(--bg-surface); box-shadow: 10px 0 30px rgba(0, 0, 0, 0.15); display: flex; flex-direction: column; transform: translateX(-100%); transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
.menu-drawer-wrapper.drawer-open .drawer-panel { transform: translateX(0); }
.drawer-header { padding: 20px; border-bottom: 1px solid var(--border-color); display: flex; justify-content: space-between; align-items: center; }
.drawer-header h3 { margin: 0; font-size: 1.2rem; font-weight: 700; color: var(--bg-dark); }
.drawer-close-btn { background: none; border: none; font-size: 1.2rem; color: var(--text-muted); cursor: pointer; }
.drawer-links-list { list-style: none; padding: 0; margin: 0; }
.drawer-links-list li { padding: 16px 24px; font-size: 1.05rem; font-weight: 600; color: var(--text-main); display: flex; align-items: center; gap: 12px; cursor: pointer; border-bottom: 1px solid #f1f5f9; transition: all 0.15s ease; }
.drawer-links-list li:hover { background-color: #f8fafc; color: var(--brand-primary); }
.drawer-links-list li.active-link { background-color: #eff6ff; color: var(--brand-primary); border-left: 4px solid var(--brand-primary); }
</style>

