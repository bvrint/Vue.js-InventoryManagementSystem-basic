<template>
  <div class="app-container">
    <Sidebar currentPage="stock" @navigate="$emit('navigate', $event)" @logout="$emit('logout')" />

    <main class="main-content">
      <header class="top-bar">
        <div class="top-bar-left">
          <h1>Stock Management</h1>
          <p class="breadcrumb">Monitor and manage inventory stock levels</p>
        </div>
        <div class="top-bar-right">
          <button class="btn btn-primary" @click="showModal = true">
            <svg
              width="16"
              height="16"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <line x1="12" y1="5" x2="12" y2="19"></line>
              <line x1="5" y1="12" x2="19" y2="12"></line>
            </svg>
            Adjust Stock
          </button>
        </div>
      </header>

      <div class="content">
        <!-- Filter Bar -->
        <div class="filter-bar">
          <div class="search-box">
            <svg
              width="18"
              height="18"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <circle cx="11" cy="11" r="8"></circle>
              <path d="m21 21-4.35-4.35"></path>
            </svg>
            <input v-model="searchQuery" type="text" placeholder="Search stock..." />
          </div>

          <select v-model="selectedStatus" class="filter-select">
            <option value="">All Status</option>
            <option value="good">Good Stock</option>
            <option value="low">Low Stock</option>
            <option value="out">Out of Stock</option>
          </select>
        </div>

        <!-- Stock Table -->
        <div class="card">
          <div class="card-header">
            <h2>Stock Levels</h2>
          </div>
          <div class="table-container">
            <table class="data-table">
              <thead>
                <tr>
                  <th>Item</th>
                  <th>Category</th>
                  <th>Current Stock</th>
                  <th>Min. Stock</th>
                  <th>Status</th>
                  <th>Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="stock in filteredStock" :key="stock.id">
                  <td>{{ stock.item }}</td>
                  <td>{{ stock.category }}</td>
                  <td>{{ stock.current }}</td>
                  <td>{{ stock.min }}</td>
                  <td>
                    <span v-if="stock.status === 'good'" class="badge success">Good Stock</span>
                    <span v-else-if="stock.status === 'low'" class="badge warning">Low Stock</span>
                    <span v-else class="badge danger">Out of Stock</span>
                  </td>
                  <td>
                    <button class="btn-icon" @click="adjustStock(stock)">
                      <svg
                        width="18"
                        height="18"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                      >
                        <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path>
                        <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path>
                      </svg>
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </main>

    <!-- Adjust Stock Modal -->
    <div v-if="showModal" class="modal" @click.self="showModal = false">
      <div class="modal-content modal-small">
        <div class="modal-header">
          <h2>Adjust Stock</h2>
          <button class="modal-close" @click="showModal = false">&times;</button>
        </div>
        <form class="modal-form" @submit.prevent="saveStock">
          <div class="form-group">
            <label>Item</label>
            <select v-model="form.item" required>
              <option value="">Select item</option>
              <option v-for="stock in stockItems" :key="stock.id">{{ stock.item }}</option>
            </select>
          </div>

          <div class="form-row">
            <div class="form-group">
              <label>Adjustment Type</label>
              <select v-model="form.type" required>
                <option value="add">Add Stock</option>
                <option value="remove">Remove Stock</option>
              </select>
            </div>
            <div class="form-group">
              <label>Quantity</label>
              <input v-model="form.quantity" type="number" min="1" required />
            </div>
          </div>

          <div class="form-group">
            <label>Reason</label>
            <textarea
              v-model="form.reason"
              rows="3"
              placeholder="Enter reason for adjustment"
            ></textarea>
          </div>

          <div class="modal-actions">
            <button type="button" class="btn btn-secondary" @click="showModal = false">
              Cancel
            </button>
            <button type="submit" class="btn btn-primary">Save</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from './Sidebar.vue'

export default {
  name: 'StockPage',
  components: {
    Sidebar,
  },
  data() {
    return {
      showModal: false,
      searchQuery: '',
      selectedStatus: '',
      form: {
        item: '',
        type: 'add',
        quantity: 1,
        reason: '',
      },
      stockItems: [
        {
          id: 1,
          item: 'Laptop Dell XPS 15',
          category: 'Electronics',
          current: 24,
          min: 10,
          status: 'good',
        },
        {
          id: 2,
          item: 'Office Chair Pro',
          category: 'Furniture',
          current: 45,
          min: 20,
          status: 'good',
        },
        {
          id: 3,
          item: 'Wireless Mouse',
          category: 'Electronics',
          current: 5,
          min: 10,
          status: 'low',
        },
        {
          id: 4,
          item: 'Mechanical Keyboard',
          category: 'Electronics',
          current: 32,
          min: 15,
          status: 'good',
        },
        {
          id: 5,
          item: '27" 4K Monitor',
          category: 'Electronics',
          current: 0,
          min: 5,
          status: 'out',
        },
        {
          id: 6,
          item: 'Standing Desk',
          category: 'Furniture',
          current: 18,
          min: 10,
          status: 'good',
        },
      ],
    }
  },
  computed: {
    filteredStock() {
      return this.stockItems.filter((stock) => {
        const matchesSearch = stock.item.toLowerCase().includes(this.searchQuery.toLowerCase())
        const matchesStatus = !this.selectedStatus || stock.status === this.selectedStatus
        return matchesSearch && matchesStatus
      })
    },
  },
  methods: {
    adjustStock(stock) {
      this.form.item = stock.item
      this.showModal = true
    },
    saveStock() {
      alert('Stock adjusted successfully!')
      this.showModal = false
      this.resetForm()
    },
    resetForm() {
      this.form = {
        item: '',
        type: 'add',
        quantity: 1,
        reason: '',
      }
    },
  },
}
</script>

<style>
/* Component specific styles will use Vue CSS variables */
</style>
