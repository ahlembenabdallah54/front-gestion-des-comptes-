<script setup>
import { ref, reactive, computed } from 'vue'
import AccountCard from './components/AccountCard.vue'

const accounts = ref([
  { name: 'Yannick', balance: 120, status: 'active' },
  { name: 'Marie', balance: 430, status: 'inactive' }
])

const newAccount = reactive({
  name: '',
  balance: 0,
  status: 'active'
})

const addAccount = () => {
  if (!newAccount.name) return
  accounts.value.push({
    name: newAccount.name,
    balance: newAccount.balance,
    status: newAccount.status
  })
  newAccount.name = ''
  newAccount.balance = 0
  newAccount.status = 'active'
}

const setStatus = (index, status) => {
  accounts.value[index].status = status
}

const deleteAccount = (index) => {
  accounts.value.splice(index, 1)
}

const totalActive = computed(() =>
  accounts.value.filter(a => a.status === 'active').reduce((s, a) => s + a.balance, 0)
)
const totalInactive = computed(() =>
  accounts.value.filter(a => a.status === 'inactive').reduce((s, a) => s + a.balance, 0)
)
</script>

<template>
  <div class="page">
    <div class="wrapper">
      <h1>💼 Gestion des Comptes</h1>

      <!-- Formulaire -->
      <div class="card form-card">
        <h2>Nouveau compte</h2>
        <div class="form-grid">
          <div class="field">
            <label>Nom</label>
            <input v-model="newAccount.name" placeholder="Ex: Thomas" />
          </div>
          <div class="field">
            <label>Solde (€)</label>
            <input v-model.number="newAccount.balance" type="number" placeholder="0" />
          </div>
          <div class="field">
            <label>Statut</label>
            <select v-model="newAccount.status">
              <option value="active">Actif</option>
              <option value="inactive">Inactif</option>
              <option value="unknown">Inconnu</option>
            </select>
          </div>
        </div>
        <button class="btn-add" @click="addAccount">+ Ajouter le compte</button>
      </div>

      <!-- Totaux -->
      <div class="totals">
        <div class="total-box active-box">
          <span class="total-label">Soldes actifs</span>
          <span class="total-amount">{{ totalActive }} €</span>
        </div>
        <div class="total-box inactive-box">
          <span class="total-label">Soldes inactifs</span>
          <span class="total-amount">{{ totalInactive }} €</span>
        </div>
      </div>

      <!-- Comptes -->
      <div class="accounts-list">
        <AccountCard
          v-for="(account, index) in accounts"
          :key="index"
          :account="account"
          :index="index"
          @set-status="setStatus"
          @delete-account="deleteAccount"
        />
      </div>
    </div>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap');

*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: 'Poppins', sans-serif;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
  min-height: 100vh;
  padding: 40px 20px;
}

.wrapper {
  max-width: 800px;
  margin: 0 auto;
}

h1 {
  color: #fff;
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 28px;
  text-align: center;
  letter-spacing: 1px;
}

.card {
  background: rgba(255,255,255,0.07);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255,255,255,0.12);
  border-radius: 16px;
  padding: 24px;
  margin-bottom: 20px;
}

.form-card h2 {
  color: #e0e0ff;
  font-size: 1.1rem;
  font-weight: 600;
  margin-bottom: 18px;
}

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 14px;
  margin-bottom: 18px;
}

.field { display: flex; flex-direction: column; gap: 6px; }

.field label {
  color: #a0b0d0;
  font-size: 0.78rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.field input,
.field select {
  background: rgba(255,255,255,0.1);
  border: 1px solid rgba(255,255,255,0.2);
  border-radius: 8px;
  padding: 10px 12px;
  color: #fff;
  font-family: 'Poppins', sans-serif;
  font-size: 0.9rem;
  outline: none;
  transition: border-color 0.2s;
}

.field input::placeholder { color: rgba(255,255,255,0.35); }
.field input:focus,
.field select:focus { border-color: #4a9eff; }
.field select option { background: #1a1a2e; color: #fff; }

.btn-add {
  background: linear-gradient(135deg, #4a9eff, #6c63ff);
  color: #fff;
  border: none;
  border-radius: 8px;
  padding: 11px 24px;
  font-family: 'Poppins', sans-serif;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: opacity 0.2s, transform 0.1s;
}
.btn-add:hover { opacity: 0.88; transform: translateY(-1px); }

.totals {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
  margin-bottom: 24px;
}

.total-box {
  border-radius: 12px;
  padding: 18px 22px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.active-box {
  background: linear-gradient(135deg, rgba(40,167,69,0.3), rgba(40,167,69,0.15));
  border: 1px solid rgba(40,167,69,0.4);
}
.inactive-box {
  background: linear-gradient(135deg, rgba(23,162,184,0.3), rgba(23,162,184,0.15));
  border: 1px solid rgba(23,162,184,0.4);
}

.total-label { color: rgba(255,255,255,0.6); font-size: 0.8rem; text-transform: uppercase; letter-spacing: 0.5px; }
.total-amount { color: #fff; font-size: 1.5rem; font-weight: 700; }
</style>