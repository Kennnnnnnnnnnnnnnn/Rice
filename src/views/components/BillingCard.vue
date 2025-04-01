<template>
  <div class="card">
    <div class="card-header pb-0 px-3">
      <div class="d-flex justify-content-between align-items-center">
        <h6 class="mb-0">User Management</h6>
        <button class="btn btn-primary btn-sm" @click="showAddUserModal = true">
          <i class="fas fa-plus me-2"></i>Add User
        </button>
      </div>
    </div>
    <div class="card-body pt-4 p-3">
      <!-- User Statistics Graph -->
      <div class="mb-4">
        <h6 class="mb-3">User Statistics</h6>
        <div class="chart-container" style="height: 250px;">
          <canvas ref="userChart"></canvas>
        </div>
      </div>

      <!-- Users List -->
      <ul class="list-group">
        <li
          v-for="user in users"
          :key="user.id"
          class="list-group-item border-0 d-flex p-4 mb-2 bg-gray-100 border-radius-lg"
        >
          <div class="d-flex flex-column">
            <h6 class="mb-3 text-sm">{{ user.name }}</h6>
            <span class="mb-2 text-xs">
              Role:
              <span class="text-dark font-weight-bold ms-sm-2">{{ user.role }}</span>
            </span>
            <span class="mb-2 text-xs">
              Email:
              <span class="text-dark ms-sm-2 font-weight-bold">{{ user.email }}</span>
            </span>
            <span class="text-xs">
              Joined:
              <span class="text-dark ms-sm-2 font-weight-bold">{{ formatDate(user.createdAt) }}</span>
            </span>
          </div>
          <div class="ms-auto text-end">
            <button
              class="btn btn-link text-danger text-gradient px-3 mb-0"
              @click="deleteUser(user.id)"
            >
              <i class="far fa-trash-alt me-2" aria-hidden="true"></i>Delete
            </button>
            <button 
              class="btn btn-link text-dark px-3 mb-0" 
              @click="editUser(user)"
            >
              <i class="fas fa-pencil-alt text-dark me-2" aria-hidden="true"></i>
              Edit
            </button>
          </div>
        </li>
      </ul>

      <!-- Add/Edit User Modal -->
      <div class="modal fade" :class="{show: showAddUserModal}" :style="{display: showAddUserModal ? 'block' : 'none'}">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{ editingUser ? 'Edit User' : 'Add New User' }}</h5>
              <button type="button" class="btn-close" @click="closeModal"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="saveUser">
                <div class="mb-3">
                  <label class="form-label">Name</label>
                  <input type="text" class="form-control" v-model="currentUser.name" required>
                </div>
                <div class="mb-3">
                  <label class="form-label">Email</label>
                  <input type="email" class="form-control" v-model="currentUser.email" required>
                </div>
                <div class="mb-3">
                  <label class="form-label">Role</label>
                  <select class="form-select" v-model="currentUser.role" required>
                    <option value="user">User</option>
                    <option value="admin">Admin</option>
                    <option value="editor">Editor</option>
                  </select>
                </div>
                <div class="d-flex justify-content-end">
                  <button type="button" class="btn btn-secondary me-2" @click="closeModal">Cancel</button>
                  <button type="submit" class="btn btn-primary">Save</button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
      <div class="modal-backdrop fade" :class="{show: showAddUserModal}" :style="{display: showAddUserModal ? 'block' : 'none'}"></div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import Chart from 'chart.js/auto';

export default {
  setup() {
    // User data store
    const users = ref([
      { id: 1, name: 'Oliver Liam', email: 'oliver@burrito.com', role: 'admin', createdAt: '2023-01-15' },
      { id: 2, name: 'Lucas Harper', email: 'lucas@stone-tech.com', role: 'editor', createdAt: '2023-02-20' },
      { id: 3, name: 'Ethan James', email: 'ethan@fiber.com', role: 'user', createdAt: '2023-03-10' }
    ]);

    // Modal and form state
    const showAddUserModal = ref(false);
    const editingUser = ref(false);
    const currentUser = reactive({
      id: null,
      name: '',
      email: '',
      role: 'user',
      createdAt: new Date().toISOString().split('T')[0]
    });

    // Chart reference
    const userChart = ref(null);

    // Format date for display
    const formatDate = (dateString) => {
      const options = { year: 'numeric', month: 'short', day: 'numeric' };
      return new Date(dateString).toLocaleDateString(undefined, options);
    };

    // Initialize chart
    const initChart = () => {
      if (userChart.value) {
        const ctx = userChart.value.getContext('2d');
        new Chart(ctx, {
          type: 'bar',
          data: {
            labels: users.value.map(user => user.name),
            datasets: [{
              label: 'User Activity',
              data: users.value.map(() => Math.floor(Math.random() * 100) + 1),
              backgroundColor: [
                'rgba(75, 192, 192, 0.6)',
                'rgba(54, 162, 235, 0.6)',
                'rgba(153, 102, 255, 0.6)'
              ],
              borderColor: [
                'rgba(75, 192, 192, 1)',
                'rgba(54, 162, 235, 1)',
                'rgba(153, 102, 255, 1)'
              ],
              borderWidth: 1
            }]
          },
          options: {
            responsive: true,
            plugins: {
              legend: {
                display: false
              }
            },
            scales: {
              y: {
                beginAtZero: true
              }
            }
          }
        });
      }
    };

    // User actions
    const editUser = (user) => {
      Object.assign(currentUser, user);
      editingUser.value = true;
      showAddUserModal.value = true;
    };

    const deleteUser = (id) => {
      if (confirm('Are you sure you want to delete this user?')) {
        users.value = users.value.filter(user => user.id !== id);
      }
    };

    const saveUser = () => {
      if (editingUser.value) {
        const index = users.value.findIndex(u => u.id === currentUser.id);
        if (index !== -1) {
          users.value[index] = {...currentUser};
        }
      } else {
        const newId = Math.max(...users.value.map(u => u.id), 0) + 1;
        users.value.push({
          ...currentUser,
          id: newId
        });
      }
      closeModal();
    };

    const closeModal = () => {
      showAddUserModal.value = false;
      editingUser.value = false;
      Object.assign(currentUser, {
        id: null,
        name: '',
        email: '',
        role: 'user',
        createdAt: new Date().toISOString().split('T')[0]
      });
    };

    // Initialize chart when component mounts
    onMounted(() => {
      initChart();
    });

    return {
      users,
      userChart,
      showAddUserModal,
      editingUser,
      currentUser,
      formatDate,
      editUser,
      deleteUser,
      saveUser,
      closeModal
    };
  }
};
</script>

<style scoped>
.modal {
  background-color: rgba(0, 0, 0, 0.5);
}
.chart-container {
  position: relative;
}
</style>



<!-- // users -->