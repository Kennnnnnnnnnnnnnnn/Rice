<template>
  <div class="container-fluid py-4">
    <div class="card">
      <div class="card-header pb-0 d-flex justify-content-between align-items-center">
        <h6>Product Stock</h6>
        <div>
          <button class="btn btn-sm btn-primary me-2">
            <i class="fas fa-plus me-1"></i> Add Product
          </button>
          <button class="btn btn-sm btn-outline-secondary">
            <i class="fas fa-filter me-1"></i> Filter
          </button>
        </div>
      </div>
      <div class="card-body px-0 pt-0 pb-2">
        <div class="table-responsive p-0">
          <table class="table align-items-center mb-0">
            <thead>
              <tr>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Name</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Category</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Price</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Plans</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Available</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Code</th>
                <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(product, index) in products" :key="index">
                <td>
                  <div class="d-flex px-2 py-1">
                    <div>
                      <img :src="product.image" class="avatar avatar-sm me-3" alt="product">
                    </div>
                    <div class="d-flex flex-column justify-content-center">
                      <h6 class="mb-0 text-sm">{{ product.name }}</h6>
                    </div>
                  </div>
                </td>
                <td>
                  <p class="text-xs font-weight-bold mb-0">{{ product.category }}</p>
                </td>
                <td>
                  <p class="text-xs font-weight-bold mb-0">{{ formatCurrency(product.price) }}</p>
                </td>
                <td>
                  <p class="text-xs font-weight-bold mb-0">{{ product.plans }}</p>
                </td>
                <td>
                  <p class="text-xs font-weight-bold mb-0">{{ product.available }}</p>
                </td>
                <td>
                  <p class="text-xs font-weight-bold mb-0">{{ product.code }}</p>
                </td>
                <td class="align-middle">
                  <button class="btn btn-sm btn-outline-primary me-1" title="Edit">
                    <i class="fas fa-pencil-alt"></i>
                  </button>
                  <button class="btn btn-sm btn-outline-danger" title="Delete">
                    <i class="fas fa-trash"></i>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="card-footer d-flex justify-content-between align-items-center">
          <p class="mb-0 text-sm">Showing {{ showingItems }} of {{ totalItems }}</p>
          <nav aria-label="Page navigation">
            <ul class="pagination pagination-sm mb-0">
              <li class="page-item">
                <a class="page-link" href="#" aria-label="Previous">
                  <span aria-hidden="true">&laquo;</span>
                </a>
              </li>
              <li class="page-item active"><a class="page-link" href="#">1</a></li>
              <li class="page-item"><a class="page-link" href="#">2</a></li>
              <li class="page-item"><a class="page-link" href="#">3</a></li>
              <li class="page-item">
                <a class="page-link" href="#" aria-label="Next">
                  <span aria-hidden="true">&raquo;</span>
                </a>
              </li>
            </ul>
          </nav>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showingItems: 7,
      totalItems: 76,
      products: [
        {
          name: "Apple Watch Sintra-4",
          category: "Digital Product",
          price: 3600.00,
          plans: "0",
          available: "0",
          code: "ADD123",
          image: "https://dummyimage.com/100x100/ccc/000"
        },
        {
          name: "Mountain Headquarter",
          category: "Digital Product",
          price: 2530.00,
          plans: "13",
          available: "0",
          code: "ADD456",
          image: "https://dummyimage.com/100x100/ccc/000"
        },
        {
          name: "Western Drive",
          category: "Fashion",
          price: 5680.00,
          plans: "035",
          available: "0",
          code: "ADD789",
          image: "https://dummyimage.com/100x100/ccc/000"
        },
        {
          name: "Samsung SAS",
          category: "Mobile",
          price: 5400.00,
          plans: "07",
          available: "0",
          code: "ADD012",
          image: "https://dummyimage.com/100x100/ccc/000"
        },
        {
          name: "Camera",
          category: "Electronic",
          price: 5420.00,
          plans: "52",
          available: "0",
          code: "ADD345",
          image: "https://dummyimage.com/100x100/ccc/000"
        },
        {
          name: "Motorized Headquarter",
          category: "Digital Product",
          price: 2530.00,
          plans: "13",
          available: "0",
          code: "ADD678",
          image: "https://dummyimage.com/100x100/ccc/000"
        },
        {
          name: "Western Drive",
          category: "Fashion",
          price: 5680.00,
          plans: "035",
          available: "0",
          code: "ADD901",
          image: "https://dummyimage.com/100x100/ccc/000"
        }
      ]
    }
  },
  methods: {
    formatCurrency(value) {
      return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD',
        minimumFractionDigits: 2
      }).format(value);
    }
  }
}
</script>

<style scoped>
.avatar {
  width: 40px;
  height: 40px;
  object-fit: cover;
  border-radius: 6px;
}

.card-header {
  padding: 1rem 1.5rem;
}

.card-footer {
  padding: 0.75rem 1.5rem;
  background-color: rgba(255, 255, 255, 0.05);
}

.pagination {
  margin-bottom: 0;
}

.page-item.active .page-link {
  background-color: #5e72e4;
  border-color: #5e72e4;
}

.page-link {
  color: #5e72e4;
}

.table-responsive {
  max-height: 500px;
  overflow-y: auto;
}

.table thead th {
  position: sticky;
  top: 0;
  background-color: white;
  z-index: 10;
}
</style>