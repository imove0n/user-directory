<script setup>
import { ref, computed, onMounted } from 'vue'

const users = ref([])
const searchQuery = ref('')
const sortAsc = ref(true)

// Fetch users from API
const fetchUsers = async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users')
    users.value = await response.json()
  } catch (error) {
    console.error('Error fetching users:', error)
  }
}

// Computed property for filtered and sorted users
const filteredAndSortedUsers = computed(() => {
  let result = [...users.value]

  // Filter by search query
  if (searchQuery.value) {
    result = result.filter(user =>
      user.name.toLowerCase().includes(searchQuery.value.toLowerCase())
    )
  }

  // Sort A-Z or Z-A
  result.sort((a, b) => {
    const nameA = a.name.toLowerCase()
    const nameB = b.name.toLowerCase()

    if (sortAsc.value) {
      return nameA.localeCompare(nameB)
    } else {
      return nameB.localeCompare(nameA)
    }
  })

  return result
})

// Toggle sort direction
const toggleSort = () => {
  sortAsc.value = !sortAsc.value
}

onMounted(() => {
  fetchUsers()
})
</script>

<template>
  <div class="app-container">
    <header class="header">
      <h1>User Directory</h1>
      <p class="subtitle">Browse and search through our user database</p>
    </header>

    <div class="controls">
      <div class="search-box">
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Search by name..."
          class="search-input"
        />
      </div>

      <button @click="toggleSort" class="sort-button">
        Sort {{ sortAsc ? 'A-Z' : 'Z-A' }}
      </button>
    </div>

    <div class="user-grid">
      <div v-for="user in filteredAndSortedUsers" :key="user.id" class="user-card">
        <div class="card-header">
          <h2 class="user-name">{{ user.name }}</h2>
          <span class="username">@{{ user.username }}</span>
        </div>

        <div class="card-body">
          <div class="info-row">
            <span class="icon">📧</span>
            <span class="info-text">{{ user.email }}</span>
          </div>

          <div class="info-row">
            <span class="icon">📞</span>
            <span class="info-text">{{ user.phone }}</span>
          </div>

          <div class="info-row">
            <span class="icon">🏢</span>
            <span class="info-text">{{ user.company.name }}</span>
          </div>

          <div class="info-row">
            <span class="icon">📍</span>
            <span class="info-text">
              {{ user.address.street }}, {{ user.address.city }}
            </span>
          </div>

          <div class="info-row">
            <span class="icon">🌐</span>
            <a :href="'http://' + user.website" target="_blank" class="website-link">
              {{ user.website }}
            </a>
          </div>
        </div>
      </div>
    </div>

    <div v-if="filteredAndSortedUsers.length === 0" class="no-results">
      <p>No users found matching "{{ searchQuery }}"</p>
    </div>
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 3rem 1.5rem;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
}

.header {
  margin-bottom: 4rem;
}

.header h1 {
  font-size: 2.75rem;
  font-weight: 700;
  margin-bottom: 0.75rem;
  letter-spacing: -0.03em;
  line-height: 1.1;
}

.subtitle {
  font-size: 1.125rem;
  opacity: 0.5;
  font-weight: 400;
}

.controls {
  display: flex;
  gap: 0.75rem;
  margin-bottom: 3rem;
  flex-wrap: wrap;
  align-items: stretch;
}

.search-box {
  flex: 1;
  min-width: 280px;
}

.search-input {
  width: 100%;
  height: 100%;
  padding: 1rem 1.25rem;
  font-size: 1rem;
  border: 2px solid #000;
  border-radius: 0;
  background: #fff;
  font-family: inherit;
  transition: all 0.15s ease;
}

.search-input:focus {
  outline: none;
  background: #f9f9f9;
  box-shadow: inset 0 0 0 1px #000;
}

.search-input::placeholder {
  color: #999;
}

.sort-button {
  padding: 1rem 2rem;
  font-size: 1rem;
  background: #000;
  color: #fff;
  border: 2px solid #000;
  border-radius: 0;
  cursor: pointer;
  font-weight: 600;
  font-family: inherit;
  transition: all 0.15s ease;
  white-space: nowrap;
}

.sort-button:hover {
  background: #fff;
  color: #000;
}

.sort-button:active {
  transform: translateY(1px);
}

.user-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(340px, 1fr));
  gap: 2rem;
  margin-bottom: 2rem;
}

.user-card {
  background: #fff;
  border: 2px solid #000;
  padding: 0;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.user-card:hover {
  transform: translate(-2px, -2px);
  box-shadow: 4px 4px 0 #000;
}

.card-header {
  background: #000;
  padding: 1.5rem;
  color: #fff;
}

.user-name {
  font-size: 1.375rem;
  font-weight: 700;
  margin-bottom: 0.375rem;
  letter-spacing: -0.02em;
}

.username {
  font-size: 1rem;
  opacity: 0.7;
  font-weight: 400;
}

.card-body {
  padding: 1.5rem;
  background: #fff;
}

.info-row {
  display: flex;
  align-items: flex-start;
  gap: 1rem;
  margin-bottom: 1rem;
  font-size: 0.95rem;
  line-height: 1.6;
}

.info-row:last-child {
  margin-bottom: 0;
}

.icon {
  font-size: 1.125rem;
  flex-shrink: 0;
  opacity: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
}

.info-text {
  line-height: 1.6;
  flex: 1;
}

.website-link {
  color: #000;
  text-decoration: none;
  border-bottom: 2px solid #000;
  font-weight: 500;
  transition: opacity 0.15s ease;
}

.website-link:hover {
  opacity: 0.6;
}

.no-results {
  text-align: center;
  padding: 4rem 2rem;
  opacity: 0.4;
  font-size: 1.125rem;
  font-weight: 500;
}

@media (max-width: 768px) {
  .app-container {
    padding: 2rem 1rem;
  }

  .header {
    margin-bottom: 2.5rem;
  }

  .header h1 {
    font-size: 2rem;
  }

  .subtitle {
    font-size: 1rem;
  }

  .user-grid {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }

  .controls {
    flex-direction: column;
    gap: 0.75rem;
  }

  .search-box {
    min-width: 100%;
  }

  .sort-button {
    width: 100%;
  }
}
</style>
