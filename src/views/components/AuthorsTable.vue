<template>
  <div class="container-fluid py-4">
    <!-- Summary Cards -->
    <div class="row mb-4">
      <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4">
        <div class="card">
          <div class="card-body p-3">
            <div class="row">
              <div class="col-8">
                <div class="numbers">
                  <p class="text-sm mb-0 text-uppercase font-weight-bold">Total Sales</p>
                  <h5 class="font-weight-bolder">{{ formatCurrency(totalSales) }}</h5>
                  <p class="mb-0">
                    <span class="text-success text-sm font-weight-bolder">+{{ salesGrowth }}%</span> vs last month
                  </p>
                </div>
              </div>
              <div class="col-4 text-end">
                <div class="icon icon-shape bg-gradient-primary shadow-primary text-center rounded-circle">
                  <i class="ni ni-money-coins text-lg opacity-10" aria-hidden="true"></i>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4">
        <div class="card">
          <div class="card-body p-3">
            <div class="row">
              <div class="col-8">
                <div class="numbers">
                  <p class="text-sm mb-0 text-uppercase font-weight-bold">Total Orders</p>
                  <h5 class="font-weight-bolder">{{ totalOrders }}</h5>
                  <p class="mb-0">
                    <span class="text-success text-sm font-weight-bolder">+{{ orderGrowth }}%</span> vs last month
                  </p>
                </div>
              </div>
              <div class="col-4 text-end">
                <div class="icon icon-shape bg-gradient-danger shadow-danger text-center rounded-circle">
                  <i class="ni ni-cart text-lg opacity-10" aria-hidden="true"></i>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-xl-3 col-sm-6 mb-xl-0 mb-4">
        <div class="card">
          <div class="card-body p-3">
            <div class="row">
              <div class="col-8">
                <div class="numbers">
                  <p class="text-sm mb-0 text-uppercase font-weight-bold">Avg. Order Value</p>
                  <h5 class="font-weight-bolder">{{ formatCurrency(avgOrderValue) }}</h5>
                  <p class="mb-0">
                    <span class="text-success text-sm font-weight-bolder">+{{ aovGrowth }}%</span> vs last month
                  </p>
                </div>
              </div>
              <div class="col-4 text-end">
                <div class="icon icon-shape bg-gradient-success shadow-success text-center rounded-circle">
                  <i class="ni ni-chart-bar-32 text-lg opacity-10" aria-hidden="true"></i>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-xl-3 col-sm-6">
        <div class="card">
          <div class="card-body p-3">
            <div class="row">
              <div class="col-8">
                <div class="numbers">
                  <p class="text-sm mb-0 text-uppercase font-weight-bold">This Month</p>
                  <h5 class="font-weight-bolder">{{ formatCurrency(monthlySales) }}</h5>
                  <p class="mb-0">
                    <span class="text-success text-sm font-weight-bolder">{{ monthlyProgress }}%</span> of target
                  </p>
                </div>
              </div>
              <div class="col-4 text-end">
                <div class="icon icon-shape bg-gradient-warning shadow-warning text-center rounded-circle">
                  <i class="ni ni-calendar-grid-58 text-lg opacity-10" aria-hidden="true"></i>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Sales Chart -->
    <div class="row mb-4">
      <div class="col-lg-12">
        <div class="card z-index-2">
          <div class="card-header pb-0">
            <h6>Sales overview</h6>
            <p class="text-sm">
              <i class="fa fa-arrow-up text-success"></i>
              <span class="font-weight-bold">{{ salesGrowth }}% more</span> than last month
            </p>
          </div>
          <div class="card-body p-3">
            <div class="chart">
              <canvas id="sales-chart" class="chart-canvas" height="300"></canvas>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Order History -->
    <div class="row">
      <div class="col-12">
        <div class="card mb-4">
          <div class="card-header pb-0 d-flex justify-content-between align-items-center">
            <h6>Order History</h6>
            <div>
              <button class="btn btn-sm btn-outline-secondary me-2">
                <i class="fas fa-download me-1"></i> Export
              </button>
              <div class="btn-group">
                <button 
                  v-for="period in timePeriods" 
                  :key="period" 
                  class="btn btn-sm"
                  :class="{'btn-primary': selectedPeriod === period, 'btn-outline-primary': selectedPeriod !== period}"
                  @click="selectedPeriod = period"
                >
                  {{ period }}
                </button>
              </div>
            </div>
          </div>
          <div class="card-body px-0 pt-0 pb-2">
            <div class="table-responsive p-0">
              <table class="table align-items-center mb-0">
                <thead>
                  <tr>
                    <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Order ID</th>
                    <th class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2">Customer</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Date</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Status</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Amount</th>
                    <th class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7">Actions</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="order in filteredOrders" :key="order.id">
                    <td class="ps-4">
                      <p class="text-xs font-weight-bold mb-0">#{{ order.id }}</p>
                    </td>
                    <td>
                      <div class="d-flex px-2 py-1">
                        <div>
                          <img :src="order.customer.avatar" class="avatar avatar-sm me-3" alt="user">
                        </div>
                        <div class="d-flex flex-column justify-content-center">
                          <h6 class="mb-0 text-sm">{{ order.customer.name }}</h6>
                          <p class="text-xs text-secondary mb-0">{{ order.customer.email }}</p>
                        </div>
                      </div>
                    </td>
                    <td class="text-center">
                      <span class="text-secondary text-xs font-weight-bold">{{ formatDate(order.date) }}</span>
                    </td>
                    <td class="text-center">
                      <span class="badge" :class="statusClass(order.status)">{{ order.status }}</span>
                    </td>
                    <td class="text-center">
                      <span class="text-secondary text-xs font-weight-bold">{{ formatCurrency(order.amount) }}</span>
                    </td>
                    <td class="text-center">
                      <button class="btn btn-sm btn-outline-info me-1" title="View">
                        <i class="fas fa-eye"></i>
                      </button>
                      <button class="btn btn-sm btn-outline-secondary" title="Invoice">
                        <i class="fas fa-file-invoice"></i>
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div class="card-footer d-flex justify-content-between align-items-center">
              <p class="mb-0 text-sm">Showing {{ showingItems }} of {{ totalOrders }}</p>
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
    </div>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';

export default {
  data() {
    return {
      totalSales: 48240.50,
      salesGrowth: 12.5,
      totalOrders: 142,
      orderGrowth: 8.3,
      avgOrderValue: 339.72,
      aovGrowth: 4.2,
      monthlySales: 15890.25,
      monthlyProgress: 63,
      showingItems: 10,
      selectedPeriod: 'All',
      timePeriods: ['Today', 'Week', 'Month', 'All'],
      orders: [
        {
          id: 'ORD-1001',
          customer: {
            name: 'John Michael',
            email: 'john@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-22',
          status: 'Completed',
          amount: 2530.00
        },
        {
          id: 'ORD-1002',
          customer: {
            name: 'Alexa Liras',
            email: 'alexa@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-21',
          status: 'Processing',
          amount: 5680.00
        },
        {
          id: 'ORD-1003',
          customer: {
            name: 'Laurent Perrier',
            email: 'laurent@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-20',
          status: 'Shipped',
          amount: 3600.00
        },
        {
          id: 'ORD-1004',
          customer: {
            name: 'Michael Levi',
            email: 'michael@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-19',
          status: 'Completed',
          amount: 5400.00
        },
        {
          id: 'ORD-1005',
          customer: {
            name: 'Richard Gran',
            email: 'richard@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-18',
          status: 'Completed',
          amount: 5420.00
        },
        {
          id: 'ORD-1006',
          customer: {
            name: 'Miriam Eric',
            email: 'miriam@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-17',
          status: 'Cancelled',
          amount: 2530.00
        },
        {
          id: 'ORD-1007',
          customer: {
            name: 'John Michael',
            email: 'john@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-16',
          status: 'Completed',
          amount: 5680.00
        },
        {
          id: 'ORD-1008',
          customer: {
            name: 'Alexa Liras',
            email: 'alexa@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-15',
          status: 'Completed',
          amount: 3600.00
        },
        {
          id: 'ORD-1009',
          customer: {
            name: 'Laurent Perrier',
            email: 'laurent@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-14',
          status: 'Processing',
          amount: 5400.00
        },
        {
          id: 'ORD-1010',
          customer: {
            name: 'Michael Levi',
            email: 'michael@creative-tim.com',
            avatar: 'https://dummyimage.com/100x100/ccc/000'
          },
          date: '2023-06-13',
          status: 'Shipped',
          amount: 5420.00
        }
      ]
    }
  },
  computed: {
    filteredOrders() {
      // In a real app, you would filter based on the selected period
      return this.orders;
    }
  },
  methods: {
    formatCurrency(value) {
      return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD',
        minimumFractionDigits: 2
      }).format(value);
    },
    formatDate(dateString) {
      const options = { year: 'numeric', month: 'short', day: 'numeric' };
      return new Date(dateString).toLocaleDateString('en-US', options);
    },
    statusClass(status) {
      const classes = {
        'Completed': 'bg-gradient-success',
        'Processing': 'bg-gradient-info',
        'Shipped': 'bg-gradient-primary',
        'Cancelled': 'bg-gradient-danger'
      };
      return classes[status] || 'bg-gradient-secondary';
    },
    initChart() {
      const ctx = document.getElementById('sales-chart').getContext('2d');
      new Chart(ctx, {
        type: 'line',
        data: {
          labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
          datasets: [{
            label: 'Sales',
            tension: 0.4,
            borderWidth: 0,
            pointRadius: 0,
            borderColor: "#5e72e4",
            backgroundColor: "#5e72e4",
            fill: true,
            data: [5000, 7500, 10000, 8500, 12000, 11000, 15000, 14000, 18000, 17000, 20000, 22000],
            maxBarThickness: 6
          }],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              display: false,
            }
          },
          interaction: {
            intersect: false,
            mode: 'index',
          },
          scales: {
            y: {
              grid: {
                drawBorder: false,
                display: true,
                drawOnChartArea: true,
                drawTicks: false,
                borderDash: [5, 5]
              },
              ticks: {
                display: true,
                padding: 10,
                color: '#fbfbfb',
                font: {
                  size: 11,
                  family: "Open Sans",
                  style: 'normal',
                  lineHeight: 2
                },
              }
            },
            x: {
              grid: {
                drawBorder: false,
                display: false,
                drawOnChartArea: false,
                drawTicks: false,
                borderDash: [5, 5]
              },
              ticks: {
                display: true,
                color: '#ccc',
                padding: 20,
                font: {
                  size: 11,
                  family: "Open Sans",
                  style: 'normal',
                  lineHeight: 2
                },
              }
            },
          },
        },
      });
    }
  },
  mounted() {
    this.initChart();
  }
}
</script>

<style scoped>
.card {
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
  border: none;
}

.card-header {
  padding: 1.5rem;
}

.card-header h6 {
  margin-bottom: 0.25rem;
}

.card-header p {
  color: #6c757d;
  margin-bottom: 0;
}

.icon-shape {
  width: 48px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.avatar {
  width: 36px;
  height: 36px;
  object-fit: cover;
  border-radius: 50%;
}

.badge {
  font-size: 0.65rem;
  font-weight: 600;
  padding: 0.5rem 0.75rem;
  border-radius: 0.25rem;
  color: white;
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

.chart {
  min-height: 300px;
}

.btn-group .btn {
  padding: 0.375rem 0.75rem;
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
</style>