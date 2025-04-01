<script setup>
import PaymentCard from "./components/PaymentCard.vue";
import BillingCard from "./components/BillingCard.vue";
import TransactionCard from "./components/TransactionCard.vue";
import InvoiceCard from "./components/InvoiceCard.vue";
import { ref } from 'vue';

const editing = ref(false);
const originalRoles = ref([]);
const roles = ref([
  {
    id: 1,
    name: 'Admin',
    permissions: {
      read: true,
      create: true,
      update: true,
      manageUsers: true,
      manageAdmins: true
    }
  },
  {
    id: 2,
    name: 'User',
    permissions: {
      read: true,
      create: false,
      update: false,
      manageUsers: false,
      manageAdmins: false
    }
  }
]);

const startEditing = () => {
  originalRoles.value = JSON.parse(JSON.stringify(roles.value));
  editing.value = true;
};

const togglePermission = (roleId, permission) => {
  if (!editing.value) return;
  
  const role = roles.value.find(r => r.id === roleId);
  if (role) {
    role.permissions[permission] = !role.permissions[permission];
  }
};

const hasChanged = (roleId, permission) => {
  if (!editing.value) return false;
  const originalRole = originalRoles.value.find(r => r.id === roleId);
  const currentRole = roles.value.find(r => r.id === roleId);
  return originalRole && currentRole && 
         originalRole.permissions[permission] !== currentRole.permissions[permission];
};

const saveChanges = () => {
  editing.value = false;
  console.log('Updated roles:', roles.value);
  // Here you would typically send the updated roles to your backend API
};

const cancelEditing = () => {
  roles.value = JSON.parse(JSON.stringify(originalRoles.value));
  editing.value = false;
};
</script>

<template>
  <div class="container-fluid">
    <!-- Roles & Permissions Card at the Top -->
    <div class="row mt-4">
      <div class="col-12">
        <div class="card">
          <div class="card-header pb-0 d-flex justify-content-between align-items-center">
            <h6>Roles and Permissions</h6>
            <div>
              <button v-if="editing" @click="cancelEditing" class="btn btn-sm btn-outline-danger me-2">
                <i class="fas fa-times me-1"></i> Cancel
              </button>
              <button v-if="editing" @click="saveChanges" class="btn btn-sm btn-success">
                <i class="fas fa-save me-1"></i> Save
              </button>
              <button v-else @click="startEditing" class="btn btn-sm btn-primary">
                <i class="fas fa-edit me-1"></i> Edit Permissions
              </button>
            </div>
          </div>
          <div class="card-body px-0 pt-0 pb-2">
            <div class="table-responsive p-0">
              <table class="table align-items-center mb-0">
                <thead>
                  <tr>
                    <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">ID</th>
                    <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Role</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Read</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Create</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Update</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Manage Users</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Manage Admins</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="role in roles" :key="role.id">
                    <td class="ps-4">
                      <p class="text-xs font-weight-bold mb-0">{{ role.id }}</p>
                    </td>
                    <td>
                      <p class="text-xs font-weight-bold mb-0">{{ role.name }}</p>
                    </td>
                    <td 
                      class="align-middle text-center permission-cell"
                      :class="{'editable': editing, 'changed': hasChanged(role.id, 'read')}"
                      @click="togglePermission(role.id, 'read')"
                    >
                      <i v-if="role.permissions.read" class="ni ni-check-bold text-success"></i>
                      <i v-else class="fas fa-times text-danger"></i>
                    </td>
                    <td 
                      class="align-middle text-center permission-cell"
                      :class="{'editable': editing, 'changed': hasChanged(role.id, 'create')}"
                      @click="togglePermission(role.id, 'create')"
                    >
                      <i v-if="role.permissions.create" class="ni ni-check-bold text-success"></i>
                      <i v-else class="fas fa-times text-danger"></i>
                    </td>
                    <td 
                      class="align-middle text-center permission-cell"
                      :class="{'editable': editing, 'changed': hasChanged(role.id, 'update')}"
                      @click="togglePermission(role.id, 'update')"
                    >
                      <i v-if="role.permissions.update" class="ni ni-check-bold text-success"></i>
                      <i v-else class="fas fa-times text-danger"></i>
                    </td>
                    <td 
                      class="align-middle text-center permission-cell"
                      :class="{'editable': editing, 'changed': hasChanged(role.id, 'manageUsers')}"
                      @click="togglePermission(role.id, 'manageUsers')"
                    >
                      <i v-if="role.permissions.manageUsers" class="ni ni-check-bold text-success"></i>
                      <i v-else class="fas fa-times text-danger"></i>
                    </td>
                    <td 
                      class="align-middle text-center permission-cell"
                      :class="{'editable': editing, 'changed': hasChanged(role.id, 'manageAdmins')}"
                      @click="togglePermission(role.id, 'manageAdmins')"
                    >
                      <i v-if="role.permissions.manageAdmins" class="ni ni-check-bold text-success"></i>
                      <i v-else class="fas fa-times text-danger"></i>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Original Content Below (modified to remove the two cards) -->
    <div class="row">
      <div class="col-lg-8">
        <div class="row mt-4">
          <div class="col-md-12 mb-4">
            <payment-card />  
          </div>
        </div>
      </div>
      <div class="col-lg-4">
        <invoice-card class="mt-4" />
      </div>
    </div>
    
    <div class="row">
      <div class="col-md-7">
        <billing-card />
      </div>
      <div class="col-md-5">
        <transaction-card />
      </div>
    </div>
  </div>
</template>

<style scoped>
.permission-cell {
  transition: all 0.2s ease;
  position: relative;
}

.permission-cell.editable {
  cursor: pointer;
}

.permission-cell.editable:hover {
  background-color: rgba(0, 0, 0, 0.05);
  transform: scale(1.05);
}

.permission-cell.changed::after {
  content: '';
  position: absolute;
  top: 2px;
  right: 2px;
  width: 6px;
  height: 6px;
  background-color: #2dce89;
  border-radius: 50%;
}

.btn-sm {
  padding: 0.25rem 0.5rem;
  font-size: 0.75rem;
}
</style>