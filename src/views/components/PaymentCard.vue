<template>
  <div class="user-search-container">
    <!-- Search Input with Floating Label -->
    <div class="search-input-container">
      <div class="search-icon">
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <circle cx="11" cy="11" r="8"></circle>
          <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
        </svg>
      </div>
      <input
        v-model="searchQuery"
        type="text"
        class="search-input"
        placeholder=" "
        @input="handleSearch"
        @focus="isFocused = true"
        @blur="isFocused = false"
      >
      <label class="search-label">Search users by name or email</label>
      <div v-if="searchQuery" class="clear-icon" @click="clearSearch">
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <line x1="18" y1="6" x2="6" y2="18"></line>
          <line x1="6" y1="6" x2="18" y2="18"></line>
        </svg>
      </div>
    </div>

    <!-- Search Results Dropdown -->
    <div v-if="isFocused && searchQuery" class="search-results-dropdown">
      <div v-if="loading" class="loading-state">
        <div class="spinner"></div>
        <span>Searching users...</span>
      </div>

      <div v-else-if="searchResults.length > 0">
        <div v-for="user in searchResults" :key="user.id" class="user-card" @click="selectUser(user)">
          <div class="user-avatar">
            <img :src="user.avatar || defaultAvatar" :alt="user.name">
          </div>
          <div class="user-info">
            <h4 class="user-name">{{ user.name }}</h4>
            <p class="user-email">{{ user.email }}</p>
            <div class="user-meta">
              <span class="user-role">{{ user.role }}</span>
              <span class="user-status" :class="user.status">{{ user.status }}</span>
            </div>
          </div>
          <div class="select-indicator">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <polyline points="9 18 15 12 9 6"></polyline>
            </svg>
          </div>
        </div>
      </div>

      <div v-else-if="searchQuery && !loading" class="empty-state">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <circle cx="11" cy="11" r="8"></circle>
          <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
          <line x1="11" y1="8" x2="11" y2="11"></line>
          <line x1="11" y1="14" x2="11" y2="14"></line>
        </svg>
        <p>No users found for "{{ searchQuery }}"</p>
        <small>Try different search terms</small>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'UserSearchBar',
  data() {
    return {
      searchQuery: '',
      searchResults: [],
      loading: false,
      isFocused: false,
      defaultAvatar: 'https://www.gravatar.com/avatar/default?s=200&d=mp'
    }
  },
  methods: {
    async handleSearch() {
      if (!this.searchQuery.trim()) {
        this.searchResults = []
        return
      }

      this.loading = true
      try {
        // Simulated API call - replace with your actual API
        await new Promise(resolve => setTimeout(resolve, 800))
        this.searchResults = this.mockSearchResults(this.searchQuery)
      } catch (error) {
        console.error('Search error:', error)
        this.searchResults = []
      } finally {
        this.loading = false
      }
    },
    mockSearchResults(query) {
      // Mock data - replace with real API response
      const mockUsers = [
        {
          id: 1,
          name: 'Alex Johnson',
          email: 'alex@example.com',
          role: 'Administrator',
          status: 'active',
          avatar: 'https://randomuser.me/api/portraits/men/32.jpg'
        },
        {
          id: 2,
          name: 'Maria Garcia',
          email: 'maria@example.com',
          role: 'Editor',
          status: 'active',
          avatar: 'https://randomuser.me/api/portraits/women/44.jpg'
        },
        {
          id: 3,
          name: 'James Smith',
          email: 'james@example.com',
          role: 'Viewer',
          status: 'pending',
          avatar: 'https://randomuser.me/api/portraits/men/67.jpg'
        }
      ]
      return mockUsers.filter(user => 
        user.name.toLowerCase().includes(query.toLowerCase()) || 
        user.email.toLowerCase().includes(query.toLowerCase())
      )
    },
    selectUser(user) {
      this.$emit('user-selected', user)
      this.searchQuery = ''
      this.searchResults = []
      this.isFocused = false
    },
    clearSearch() {
      this.searchQuery = ''
      this.searchResults = []
    }
  }
}
</script>

<style scoped>
.user-search-container {
  position: relative;
  max-width: 600px;
  margin: 24px auto 16px; /* Added top margin of 24px and bottom margin of 16px */
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  padding-top: 8px; /* Added inner top spacing */
}

.search-input-container {
  position: relative;
  margin-bottom: 8px;
}

.search-input {
  width: 100%;
  padding: 16px 48px 16px 48px;
  font-size: 16px;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  background-color: #f8fafc;
  transition: all 0.3s ease;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
}

.search-input:focus {
  outline: none;
  border-color: #6366f1;
  background-color: #ffffff;
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.1);
}

.search-label {
  position: absolute;
  left: 48px;
  top: 16px;
  color: #94a3b8;
  transition: all 0.2s ease;
  pointer-events: none;
  background-color: #f8fafc;
  padding: 0 4px;
}

.search-input:focus + .search-label,
.search-input:not(:placeholder-shown) + .search-label {
  transform: translateY(-28px) scale(0.9);
  color: #6366f1;
  background-color: white;
}

.search-icon {
  position: absolute;
  left: 16px;
  top: 50%;
  transform: translateY(-50%);
  color: #94a3b8;
}

.clear-icon {
  position: absolute;
  right: 16px;
  top: 50%;
  transform: translateY(-50%);
  color: #94a3b8;
  cursor: pointer;
  transition: color 0.2s;
}

.clear-icon:hover {
  color: #64748b;
}

.search-results-dropdown {
  position: absolute;
  width: 100%;
  max-height: 400px;
  overflow-y: auto;
  background: white;
  border-radius: 12px;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
  z-index: 50;
  margin-top: 8px;
  border: 1px solid #e2e8f0;
}

.loading-state {
  display: flex;
  align-items: center;
  padding: 20px;
  color: #64748b;
}

.spinner {
  width: 20px;
  height: 20px;
  border: 2px solid #e2e8f0;
  border-top-color: #6366f1;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin-right: 12px;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.user-card {
  display: flex;
  align-items: center;
  padding: 16px;
  cursor: pointer;
  transition: background-color 0.2s;
  border-bottom: 1px solid #f1f5f9;
}

.user-card:last-child {
  border-bottom: none;
}

.user-card:hover {
  background-color: #f8fafc;
}

.user-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  overflow: hidden;
  margin-right: 16px;
  flex-shrink: 0;
}

.user-avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.user-info {
  flex-grow: 1;
  min-width: 0;
}

.user-name {
  font-size: 15px;
  font-weight: 600;
  margin: 0 0 4px 0;
  color: #1e293b;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.user-email {
  font-size: 13px;
  color: #64748b;
  margin: 0 0 6px 0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.user-meta {
  display: flex;
  gap: 8px;
}

.user-role, .user-status {
  font-size: 12px;
  padding: 2px 8px;
  border-radius: 9999px;
}

.user-role {
  background-color: #e0f2fe;
  color: #0369a1;
}

.user-status {
  background-color: #dcfce7;
  color: #166534;
}

.user-status.pending {
  background-color: #fef3c7;
  color: #92400e;
}

.select-indicator {
  color: #94a3b8;
  margin-left: 12px;
}

.empty-state {
  padding: 24px;
  text-align: center;
  color: #64748b;
}

.empty-state svg {
  margin-bottom: 12px;
  color: #cbd5e1;
}

.empty-state p {
  margin: 0 0 4px 0;
  font-weight: 500;
}

.empty-state small {
  font-size: 12px;
  color: #94a3b8;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .user-search-container {
    margin-top: 16px;
    margin-bottom: 12px;
    padding: 0 12px;
  }
  
  .search-input {
    padding: 12px 40px;
  }
  
  .search-label {
    left: 40px;
  }
  
  .search-icon, .clear-icon {
    width: 16px;
    height: 16px;
  }
}
</style>

<!-- Search user and email -->