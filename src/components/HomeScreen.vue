<template>
  <div class="view-screen home-screen-view">
    <div class="hero-card">
      <h1>QuickPOS Core v2.5</h1>
      <p>Enterprise mobile point-of-sale node terminal interface configuration.</p>
      <div class="hero-actions-grid">
        <button class="btn-hero-primary" @click="$emit('navigate', 'pos')">
          Launch POS Terminal
        </button>
        <button class="btn-hero-secondary" @click="$emit('navigate', 'about')">
          View System Specs
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
defineEmits(['navigate'])
</script>

<style scoped>
.view-screen { flex: 1; overflow-y: auto; padding: 24px; display: flex; align-items: center; justify-content: center; }
.hero-card { background: var(--bg-surface); padding: 32px 24px; border-radius: var(--radius-lg); box-shadow: 0 4px 20px rgba(0,0,0,0.05); text-align: center; max-width: 480px; width: 100%; }
.hero-card h1 { font-size: 1.8rem; margin: 0 0 12px 0; font-weight: 800; color: var(--bg-dark); }
.hero-card p { color: var(--text-muted); line-height: 1.5; margin: 0 0 24px 0; font-size: 0.95rem; }
.hero-actions-grid { display: flex; flex-direction: column; gap: 12px; }
.btn-hero-primary { width: 100%; padding: 14px; background: var(--brand-primary); color: white; border: none; border-radius: var(--radius-md); font-weight: 600; font-size: 1rem; cursor: pointer; }
.btn-hero-secondary { width: 100%; padding: 14px; background: #f1f5f9; color: var(--text-main); border: none; border-radius: var(--radius-md); font-weight: 600; font-size: 1rem; cursor: pointer; }
</style>

