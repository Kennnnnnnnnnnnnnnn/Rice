<template>
  <div class="card">
    <div class="card-header pb-0 px-3">
      <div class="d-flex justify-content-between align-items-center">
        <h6 class="mb-0">Admin Management</h6>
        <button class="btn btn-primary btn-sm" @click="showAddAdminModal = true">
          <i class="fas fa-plus me-2"></i>Add Admin
        </button>
      </div>
    </div>
    <div class="card-body pt-4 p-3">
      <!-- Admin Statistics Graph -->
      <div class="mb-4">
        <h6 class="mb-3">Admin Activity</h6>
        <div class="chart-container" style="height: 250px;">
          <canvas ref="adminChart"></canvas>
        </div>
      </div>

      <!-- Admins List -->
      <ul class="list-group">
        <li
          v-for="admin in admins"
          :key="admin.id"
          class="list-group-item border-0 d-flex p-4 mb-2 bg-gray-100 border-radius-lg"
        >
          <div class="d-flex flex-column">
            <h6 class="mb-3 text-sm">{{ admin.name }}</h6>
            <span class="mb-2 text-xs">
              Access Level:
              <span class="text-dark font-weight-bold ms-sm-2">{{ admin.accessLevel }}</span>
            </span>
            <span class="mb-2 text-xs">
              Email:
              <span class="text-dark ms-sm-2 font-weight-bold">{{ admin.email }}</span>
            </span>
            <span class="text-xs">
              Last Active:
              <span class="text-dark ms-sm-2 font-weight-bold">{{ formatDate(admin.lastActive) }}</span>
            </span>
            <span class="text-xs">
              Permissions:
              <span class="text-dark ms-sm-2 font-weight-bold">{{ admin.permissions.join(', ') }}</span>
            </span>
          </div>
          <div class="ms-auto text-end">
            <button
              v-if="admin.id !== currentAdminId"
              class="btn btn-link text-danger text-gradient px-3 mb-0"
              @click="deleteAdmin(admin.id)"
            >
              <i class="far fa-trash-alt me-2" aria-hidden="true"></i>Revoke Access
            </button>
            <button 
              class="btn btn-link text-dark px-3 mb-0" 
              @click="editAdmin(admin)"
            >
              <i class="fas fa-pencil-alt text-dark me-2" aria-hidden="true"></i>
              Edit
            </button>
          </div>
        </li>
      </ul>

      <!-- Add/Edit Admin Modal -->
      <div class="modal fade" :class="{show: showAddAdminModal}" :style="{display: showAddAdminModal ? 'block' : 'none'}">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{ editingAdmin ? 'Edit Admin' : 'Add New Admin' }}</h5>
              <button type="button" class="btn-close" @click="closeModal"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="saveAdmin">
                <div class="mb-3">
                  <label class="form-label">Name</label>
                  <input type="text" class="form-control" v-model="currentAdmin.name" required>
                </div>
                <div class="mb-3">
                  <label class="form-label">Email</label>
                  <input type="email" class="form-control" v-model="currentAdmin.email" required>
                </div>
                <div class="mb-3">
                  <label class="form-label">Access Level</label>
                  <select class="form-select" v-model="currentAdmin.accessLevel" required>
                    <option value="super">Super Admin</option>
                    <option value="system">System Admin</option>
                    <option value="content">Content Admin</option>
                  </select>
                </div>
                <div class="mb-3">
                  <label class="form-label">Permissions</label>
                  <div class="form-check" v-for="permission in availablePermissions" :key="permission">
                    <input 
                      class="form-check-input" 
                      type="checkbox" 
                      :id="'perm-'+permission"
                      :value="permission"
                      v-model="currentAdmin.permissions"
                    >
                    <label class="form-check-label" :for="'perm-'+permission">
                      {{ permission }}
                    </label>
                  </div>
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
      <div class="modal-backdrop fade" :class="{show: showAddAdminModal}" :style="{display: showAddAdminModal ? 'block' : 'none'}"></div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import Chart from 'chart.js/auto';

export default {
  setup() {
    // Admin data store
    const admins = ref([
      { 
        id: 1, 
        name: 'Super Admin', 
        email: 'super@admin.com', 
        accessLevel: 'super', 
        lastActive: new Date().toISOString(),
        permissions: ['all']
      },
      { 
        id: 2, 
        name: 'System Admin', 
        email: 'system@admin.com', 
        accessLevel: 'system', 
        lastActive: '2023-05-20T10:30:00',
        permissions: ['server', 'database', 'backups']
      },
      { 
        id: 3, 
        name: 'Content Admin', 
        email: 'content@admin.com', 
        accessLevel: 'content', 
        lastActive: '2023-05-18T15:45:00',
        permissions: ['posts', 'media', 'comments']
      }
    ]);

    // Current admin ID (simulated)
    const currentAdminId = ref(1);

    // Modal and form state
    const showAddAdminModal = ref(false);
    const editingAdmin = ref(false);
    const availablePermissions = ref([
      'all',
      'server',
      'database',
      'backups',
      'users',
      'posts',
      'media',
      'comments',
      'settings',
      'reports'
    ]);
    
    const currentAdmin = reactive({
      id: null,
      name: '',
      email: '',
      accessLevel: 'system',
      lastActive: new Date().toISOString(),
      permissions: []
    });

    // Chart reference
    const adminChart = ref(null);

    // Format date for display
    const formatDate = (dateString) => {
      const options = { year: 'numeric', month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' };
      return new Date(dateString).toLocaleDateString(undefined, options);
    };

    // Initialize chart
    const initChart = () => {
      if (adminChart.value) {
        const ctx = adminChart.value.getContext('2d');
        new Chart(ctx, {
          type: 'line',
          data: {
            labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
            datasets: [{
              label: 'Admin Actions',
              data: [12, 19, 15, 27, 24, 32],
              fill: false,
              borderColor: 'rgb(75, 192, 192)',
              tension: 0.1
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

    // Admin actions
    const editAdmin = (admin) => {
      Object.assign(currentAdmin, admin);
      editingAdmin.value = true;
      showAddAdminModal.value = true;
    };

    const deleteAdmin = (id) => {
      if (confirm('Are you sure you want to revoke this admin access?')) {
        admins.value = admins.value.filter(admin => admin.id !== id);
      }
    };

    const saveAdmin = () => {
      if (editingAdmin.value) {
        const index = admins.value.findIndex(a => a.id === currentAdmin.id);
        if (index !== -1) {
          admins.value[index] = {...currentAdmin};
        }
      } else {
        const newId = Math.max(...admins.value.map(a => a.id), 0) + 1;
        admins.value.push({
          ...currentAdmin,
          id: newId
        });
      }
      closeModal();
    };

    const closeModal = () => {
      showAddAdminModal.value = false;
      editingAdmin.value = false;
      Object.assign(currentAdmin, {
        id: null,
        name: '',
        email: '',
        accessLevel: 'system',
        lastActive: new Date().toISOString(),
        permissions: []
      });
    };

    // Initialize chart when component mounts
    onMounted(() => {
      initChart();
    });

    return {
      admins,
      currentAdminId,
      adminChart,
      showAddAdminModal,
      editingAdmin,
      currentAdmin,
      availablePermissions,
      formatDate,
      editAdmin,
      deleteAdmin,
      saveAdmin,
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
.list-group-item {
  transition: all 0.3s ease;
}
.list-group-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
</style>