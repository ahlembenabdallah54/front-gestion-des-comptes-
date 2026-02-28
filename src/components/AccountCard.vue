<script setup>
import { ref } from 'vue'

const props = defineProps({
  account: Object,
  index: Number
})

const emit = defineEmits(['set-status', 'delete-account'])

const showModal = ref(false)

const confirmDelete = () => {
  emit('delete-account', props.index)
  showModal.value = false
}

const statusConfig = {
  active:   { label: 'Actif',   color: '#28a745', bg: 'rgba(40,167,69,0.12)',  border: 'rgba(40,167,69,0.3)'  },
  inactive: { label: 'Inactif', color: '#17a2b8', bg: 'rgba(23,162,184,0.12)', border: 'rgba(23,162,184,0.3)' },
  unknown:  { label: 'Inconnu', color: '#fd7e14', bg: 'rgba(253,126,20,0.12)', border: 'rgba(253,126,20,0.3)'  },
}

const config = (status) => statusConfig[status] || statusConfig.unknown
</script>

<template>
  <div class="account-card" :style="{
    background: `linear-gradient(135deg, ${config(account.status).bg}, rgba(255,255,255,0.04))`,
    borderColor: config(account.status).border
  }">
    <div class="card-header">
      <div>
        <h3>{{ account.name }}</h3>
        <p class="balance">{{ account.balance.toLocaleString() }} €</p>
      </div>
      <span class="badge" :style="{ background: config(account.status).color }">
        {{ config(account.status).label }}
      </span>
    </div>

    <div class="btn-group">
      <button class="btn btn-active"   @click="emit('set-status', index, 'active')">✓ Actif</button>
      <button class="btn btn-inactive" @click="emit('set-status', index, 'inactive')">⏸ Inactif</button>
      <button class="btn btn-unknown"  @click="emit('set-status', index, 'unknown')">? Inconnu</button>
      <button class="btn btn-delete"   @click="showModal = true">🗑 Supprimer</button>
    </div>
  </div>

  <!-- Modal de confirmation -->
  <Teleport to="body">
    <div class="modal-overlay" v-if="showModal" @click.self="showModal = false">
      <div class="modal">
        <div class="modal-icon">⚠️</div>
        <h3>Confirmer la suppression</h3>
        <p>Êtes-vous sûr de vouloir supprimer le compte de <strong>{{ account.name }}</strong> ? Cette action est irréversible.</p>
        <div class="modal-actions">
          <button class="btn-cancel" @click="showModal = false">Annuler</button>
          <button class="btn-confirm" @click="confirmDelete">Oui, supprimer</button>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<style scoped>
.account-card {
  border: 1px solid;
  border-radius: 16px;
  padding: 20px 24px;
  margin-bottom: 16px;
  transition: transform 0.2s;
}

.account-card:hover { transform: translateY(-2px); }

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 18px;
}

h3 {
  color: #fff;
  font-size: 1.15rem;
  font-weight: 600;
  margin-bottom: 4px;
}

.balance {
  color: rgba(255,255,255,0.7);
  font-size: 1.4rem;
  font-weight: 700;
}

.badge {
  color: #fff;
  font-size: 0.75rem;
  font-weight: 600;
  padding: 4px 12px;
  border-radius: 20px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.btn-group {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.btn {
  padding: 8px 16px;
  border: none;
  border-radius: 8px;
  color: #fff;
  font-family: 'Poppins', sans-serif;
  font-size: 0.82rem;
  font-weight: 500;
  cursor: pointer;
  transition: opacity 0.2s, transform 0.1s;
}

.btn:hover { opacity: 0.82; transform: translateY(-1px); }

.btn-active   { background: #28a745; }
.btn-inactive { background: #17a2b8; }
.btn-unknown  { background: #fd7e14; }
.btn-delete   { background: #dc3545; }

/* Modal */
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.65);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  animation: fadeIn 0.2s ease;
}

.modal {
  background: #1e2a45;
  border: 1px solid rgba(255,255,255,0.15);
  border-radius: 20px;
  padding: 36px 32px;
  max-width: 400px;
  width: 90%;
  text-align: center;
  animation: slideUp 0.25s ease;
}

.modal-icon {
  font-size: 2.5rem;
  margin-bottom: 14px;
}

.modal h3 {
  color: #fff;
  font-size: 1.2rem;
  font-weight: 700;
  margin-bottom: 12px;
}

.modal p {
  color: rgba(255,255,255,0.65);
  font-size: 0.9rem;
  line-height: 1.6;
  margin-bottom: 28px;
}

.modal p strong {
  color: #fff;
  font-weight: 600;
}

.modal-actions {
  display: flex;
  gap: 12px;
  justify-content: center;
}

.btn-cancel {
  padding: 10px 24px;
  border-radius: 10px;
  border: 1px solid rgba(255,255,255,0.2);
  background: transparent;
  color: #fff;
  font-family: 'Poppins', sans-serif;
  font-size: 0.9rem;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.2s;
}
.btn-cancel:hover { background: rgba(255,255,255,0.08); }

.btn-confirm {
  padding: 10px 24px;
  border-radius: 10px;
  border: none;
  background: #dc3545;
  color: #fff;
  font-family: 'Poppins', sans-serif;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: opacity 0.2s, transform 0.1s;
}
.btn-confirm:hover { opacity: 0.85; transform: translateY(-1px); }

@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@keyframes slideUp {
  from { opacity: 0; transform: translateY(30px); }
  to   { opacity: 1; transform: translateY(0); }
}
</style>