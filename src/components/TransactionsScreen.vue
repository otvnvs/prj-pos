<template>
  <div class="view-screen transaction-screen-view">
    <div class="ledger-container">
      
      <!-- Empty state scenario when zero cash outs have executed -->
      <div v-if="history.length === 0" class="empty-ledger-card">
        <span class="empty-icon"></span>
        <h3>No Transactions Yet</h3>
        <p>Completed customer checkouts processed at the terminal node will register records here automatically.</p>
      </div>

      <!-- Historical Record Loop Matrix -->
      <div v-else class="transactions-stack-list">
        <div 
          v-for="invoice in history" 
          :key="invoice.id" 
          class="invoice-receipt-card"
        >
          <div class="receipt-header-row">
            <div class="invoice-meta-id">
              <span class="id-tag">{{ invoice.id }}</span>
              <span class="time-stamp">Today, {{ invoice.time }}</span>
            </div>
            <div class="invoice-grand-total">
              R{{ invoice.total.toFixed(2) }}
            </div>
          </div>

          <div class="receipt-details-body">
            <div class="items-summary-label">
              Items Profile Array Breakdown ({{ invoice.itemsCount }} count):
            </div>
            <ul class="receipt-lines-breakdown">
              <li v-for="line in invoice.lines" :key="line.id">
                <span class="line-name-qty">{{ line.name }} <strong class="multiply-factor">x{{ line.quantity }}</strong></span>
                <span class="line-sum-price">R{{ (line.price * line.quantity).toFixed(2) }}</span>
              </li>
            </ul>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script setup>
defineProps({
  history: { type: Array, required: true }
})
</script>

<style scoped>
.transaction-screen-view { flex: 1; overflow-y: auto; padding: 16px; background-color: var(--bg-primary); display: block; }
.ledger-container { max-width: 600px; margin: 0 auto; width: 100%; padding-bottom: 40px; }

.empty-ledger-card {
  background: var(--bg-surface); padding: 40px 24px; border-radius: var(--radius-lg);
  box-shadow: 0 4px 20px rgba(0,0,0,0.02); text-align: center; margin-top: 40px;
}
.empty-icon { font-size: 2.5rem; display: block; margin-bottom: 12px; }
.empty-ledger-card h3 { margin: 0 0 8px 0; color: var(--bg-dark); }
.empty-ledger-card p { color: var(--text-muted); font-size: 0.9rem; line-height: 1.5; margin: 0; }

.transactions-stack-list { display: flex; flex-direction: column; gap: 16px; margin-top: 8px; }

/* Clean styling inspired by physical thermal receipt outlines */
.invoice-receipt-card {
  background: var(--bg-surface); border-radius: var(--radius-md);
  padding: 16px; border: 1px solid var(--border-color);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.02);
}

.receipt-header-row {
  display: flex; justify-content: space-between; align-items: flex-start;
  border-bottom: 1px dashed var(--border-color); padding-bottom: 12px; margin-bottom: 12px;
}

.invoice-meta-id { display: flex; flex-direction: column; gap: 4px; }
.id-tag { font-weight: 700; font-size: 1.05rem; color: var(--bg-dark); }
.time-stamp { font-size: 0.8rem; color: var(--text-muted); }

.invoice-grand-total { font-weight: 800; font-size: 1.2rem; color: var(--brand-primary); }

.items-summary-label { font-size: 0.8rem; color: var(--text-muted); font-weight: 600; text-transform: uppercase; letter-spacing: 0.5px; margin-bottom: 8px; }

.receipt-lines-breakdown { list-style: none; padding: 0; margin: 0; display: flex; flex-direction: column; gap: 6px; }
.receipt-lines-breakdown li { display: flex; justify-content: space-between; font-size: 0.95rem; color: var(--text-main); }
.line-name-qty { display: flex; gap: 8px; align-items: center; }
.multiply-factor { color: var(--brand-primary); font-size: 0.85rem; }
.line-sum-price { font-weight: 500; }
</style>

