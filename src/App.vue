<template>
  <div class="dashboard">
    <h1>📊 EY Nottingham Spirk Leadboard</h1>
    
    <div v-if="loading" class="status">Loading response data...</div>
    <div v-else-if="error" class="status error">{{ error }}</div>

    <div v-else>
      <h2>Live Entries</h2>
      <table class="data-table">
        <thead>
          <tr>
            <th>Name</th>
            <th>School</th>
            <th>Favorite Tech</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in surveys" :key="item.id">
            <td>{{ item.name }}</td>
            <td>{{ item.school }}</td>
            <td>{{ item.favorite_tech }}</td>
          </tr>
        </tbody>
      </table>
      <div class="stats-grid">
        <div class="card">
          <h3>Total Submissions</h3>
          <p class="stat-number">{{ surveys.length }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { supabase } from './supabase'

const surveys = ref([])
const loading = ref(true)
const error = ref(null)

const fetchSurveys = async () => {
  try {
    loading.value = true
    const { data, error: fetchErr } = await supabase
      .from('surveys')
      .select('*')
      .order('id', { ascending: false })

    if (fetchErr) throw fetchErr
    surveys.value = data
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  setInterval(fetchSurveys, 10000);
})
</script>

<style scoped>
.dashboard {
  font-family: system-ui, sans-serif;
  max-width: 800px;
  margin: 2rem auto;
  padding: 1rem;
}
.card {
  background: #f4f4f5;
  padding: 1rem;
  border-radius: 8px;
  margin-bottom: 2rem;
  width: 200px;
}
.stat-number {
  font-size: 2rem;
  font-weight: bold;
  margin: 0;
}
.data-table {
  width: 100%;
  border-collapse: collapse;
}
.data-table th, .data-table td {
  border: 1px solid #e4e4e7;
  padding: 0.75rem;
  text-align: left;
}
.data-table th {
  background-color: #f4f4f5;
}
.error {
  color: red;
}
</style>