<template>
  <div class="view-screen about-screen-view">
    <div class="about-content-card">
      <h2>About QuickPOS</h2>
      <p>A modular, highly optimized responsive software platform designed for retail distribution nodes.</p>
      <div class="specs-list">
        <div class="spec-row"><span>Framework:</span> <strong>Vue 3 (Composition API)</strong></div>
        <div class="spec-row"><span>Build Engine:</span> <strong>Vite Production Bundler</strong></div>
        <div class="spec-row"><span>Design System:</span> <strong>Mobile-First Native Matrix</strong></div>
      </div>
      <button class="btn-back-home" @click="$emit('navigate', 'home')">Return Home</button>
    </div>
  </div>
</template>

<script setup>
defineEmits(['navigate'])
</script>

<style scoped>
.view-screen { flex: 1; overflow-y: auto; padding: 24px; display: flex; align-items: center; justify-content: center; }
.about-content-card { background: var(--bg-surface); padding: 28px; border-radius: var(--radius-lg); box-shadow: 0 4px 20px rgba(0,0,0,0.05); max-width: 480px; width: 100%; }
.about-content-card h2 { margin: 0 0 12px 0; font-size: 1.5rem; font-weight: 700; }
.about-content-card p { color: var(--text-muted); font-size: 0.95rem; line-height: 1.5; margin: 0 0 20px 0; }
.specs-list { background: #f1f5f9; border-radius: var(--radius-md); padding: 16px; margin-bottom: 24px; display: flex; flex-direction: column; gap: 10px; }
.spec-row { display: flex; justify-content: space-between; font-size: 0.9rem; }
.spec-row span { color: var(--text-muted); }
.btn-back-home { width: 100%; padding: 12px; background: none; border: 2px solid var(--border-color); border-radius: var(--radius-md); font-weight: 600; cursor: pointer; color: var(--text-main); }
</style>

