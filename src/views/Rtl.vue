<template>
  <div class="container-fluid py-4">
    <div class="card">
      <div class="card-header pb-0">
        <h5>Create New Product</h5>
        <p class="text-sm mb-0">Fill in the form below to add a new product to your inventory</p>
      </div>
      <div class="card-body">
        <form @submit.prevent="submitForm">
          <div class="row">
            <div class="col-md-6">
              <div class="form-group">
                <label class="form-control-label">Product Name</label>
                <input 
                  v-model="product.name" 
                  type="text" 
                  class="form-control" 
                  placeholder="e.g. Apple Watch Sintra-4"
                  required
                >
              </div>
            </div>
            <div class="col-md-6">
              <div class="form-group">
                <label class="form-control-label">Category</label>
                <select v-model="product.category" class="form-control" required>
                  <option value="" disabled selected>Select a category</option>
                  <option value="Digital Product">Digital Product</option>
                  <option value="Fashion">Fashion</option>
                  <option value="Mobile">Mobile</option>
                  <option value="Electronic">Electronic</option>
                  <option value="Other">Other</option>
                </select>
              </div>
            </div>
          </div>

          <div class="row mt-3">
            <div class="col-md-4">
              <div class="form-group">
                <label class="form-control-label">Price ($)</label>
                <input 
                  v-model="product.price" 
                  type="number" 
                  step="0.01" 
                  class="form-control" 
                  placeholder="0.00"
                  required
                >
              </div>
            </div>
            <div class="col-md-4">
              <div class="form-group">
                <label class="form-control-label">Plans</label>
                <input 
                  v-model="product.plans" 
                  type="text" 
                  class="form-control" 
                  placeholder="e.g. 035"
                >
              </div>
            </div>
            <div class="col-md-4">
              <div class="form-group">
                <label class="form-control-label">Available Stock</label>
                <input 
                  v-model="product.available" 
                  type="number" 
                  class="form-control" 
                  placeholder="0"
                  required
                >
              </div>
            </div>
          </div>

          <div class="row mt-3">
            <div class="col-md-6">
              <div class="form-group">
                <label class="form-control-label">Product Code</label>
                <input 
                  v-model="product.code" 
                  type="text" 
                  class="form-control" 
                  placeholder="e.g. ADD123"
                  required
                >
              </div>
            </div>
            <div class="col-md-6">
              <div class="form-group">
                <label class="form-control-label">Product Image</label>
                <div class="custom-file">
                  <input 
                    type="file" 
                    class="custom-file-input" 
                    id="productImage" 
                    accept="image/*"
                    @change="handleImageUpload"
                  >
                  <label class="custom-file-label" for="productImage">
                    {{ product.image ? product.image.name : 'Choose image...' }}
                  </label>
                </div>
              </div>
            </div>
          </div>

          <div class="row mt-3">
            <div class="col-12">
              <div class="form-group">
                <label class="form-control-label">Description</label>
                <textarea 
                  v-model="product.description" 
                  class="form-control" 
                  rows="3" 
                  placeholder="Product description..."
                ></textarea>
              </div>
            </div>
          </div>

          <div class="row mt-4">
            <div class="col-12 d-flex justify-content-end">
              <button 
                type="button" 
                class="btn btn-outline-secondary me-3"
                @click="cancelCreation"
              >
                Cancel
              </button>
              <button 
                type="submit" 
                class="btn btn-primary"
                :disabled="isSubmitting"
              >
                <span v-if="isSubmitting">
                  <i class="fas fa-spinner fa-spin me-1"></i> Saving...
                </span>
                <span v-else>
                  <i class="fas fa-save me-1"></i> Save Product
                </span>
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isSubmitting: false,
      product: {
        name: '',
        category: '',
        price: 0,
        plans: '',
        available: 0,
        code: '',
        image: null,
        description: ''
      }
    }
  },
  methods: {
    handleImageUpload(event) {
      const file = event.target.files[0];
      if (file) {
        this.product.image = file;
      }
    },
    submitForm() {
      this.isSubmitting = true;
      
      // Here you would typically send the data to your backend API
      console.log('Creating product:', this.product);
      
      // Simulate API call
      setTimeout(() => {
        this.isSubmitting = false;
        this.$notify({
          title: 'Success',
          message: 'Product created successfully!',
          type: 'success'
        });
        this.resetForm();
      }, 1500);
    },
    resetForm() {
      this.product = {
        name: '',
        category: '',
        price: 0,
        plans: '',
        available: 0,
        code: '',
        image: null,
        description: ''
      };
      // Reset file input
      const fileInput = document.getElementById('productImage');
      if (fileInput) fileInput.value = '';
    },
    cancelCreation() {
      this.resetForm();
      this.$emit('cancel');
    }
  }
}
</script>

<style scoped>
.card-header {
  padding: 1.5rem;
}

.card-header h5 {
  margin-bottom: 0.25rem;
}

.card-header p {
  color: #6c757d;
  margin-bottom: 0;
}

.form-control-label {
  font-size: 0.875rem;
  font-weight: 600;
  color: #495057;
  margin-bottom: 0.5rem;
  display: block;
}

.form-control, .custom-file-input {
  border-radius: 0.375rem;
  padding: 0.625rem 0.75rem;
}

.custom-file-label {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  padding: 0.625rem 0.75rem;
}

.btn {
  padding: 0.625rem 1.25rem;
  font-size: 0.875rem;
  border-radius: 0.375rem;
}

.btn-primary {
  background-color: #5e72e4;
  border-color: #5e72e4;
}

.btn-primary:hover {
  background-color: #4a5fd1;
  border-color: #4a5fd1;
}

.btn-outline-secondary {
  color: #6c757d;
  border-color: #dee2e6;
}

.btn-outline-secondary:hover {
  background-color: #f8f9fa;
}

textarea.form-control {
  min-height: 100px;
}
</style>