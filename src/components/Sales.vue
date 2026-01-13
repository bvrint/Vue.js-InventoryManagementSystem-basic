<template>
  <div class="app-container">
    <Sidebar currentPage="sales" @navigate="$emit('navigate', $event)" @logout="$emit('logout')" />

    <main class="main-content">
      <header class="top-bar">
        <div class="top-bar-left">
          <h1>Sales Management</h1>
          <p class="breadcrumb">Track and manage sales transactions</p>
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
            New Sale
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
            <input v-model="searchQuery" type="text" placeholder="Search sales..." />
          </div>

          <input v-model="filterDate" type="date" class="filter-input" />
        </div>

        <!-- Sales Table -->
        <div class="card">
          <div class="card-header">
            <h2>Recent Sales</h2>
          </div>
          <div class="table-container">
            <table class="data-table">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Item</th>
                  <th>Quantity</th>
                  <th>Price</th>
                  <th>Total</th>
                  <th>Date</th>
                  <th>Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="sale in filteredSales" :key="sale.id">
                  <td>#{{ sale.id }}</td>
                  <td>{{ sale.item }}</td>
                  <td>{{ sale.quantity }}</td>
                  <td>${{ sale.price.toFixed(2) }}</td>
                  <td>${{ (sale.quantity * sale.price).toFixed(2) }}</td>
                  <td>{{ sale.date }}</td>
                  <td>
                    <button class="btn-icon" title="View" @click="viewSale(sale)">
                      <svg
                        width="18"
                        height="18"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                      >
                        <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                        <circle cx="12" cy="12" r="3"></circle>
                      </svg>
                    </button>
                    <button class="btn-icon" title="Edit" @click="editSale(sale)">
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
                    <button class="btn-icon danger" title="Delete" @click="deleteSale(sale)">
                      <svg
                        width="18"
                        height="18"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                      >
                        <polyline points="3 6 5 6 21 6"></polyline>
                        <path
                          d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"
                        ></path>
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

    <!-- Receipt View Modal -->
    <div v-if="showViewModal && viewingSale" class="modal" @click.self="showViewModal = false">
      <div class="receipt-modal">
        <div class="receipt-header">
          <h2>INVOICE</h2>
          <button class="modal-close" @click="showViewModal = false">&times;</button>
        </div>
        <div class="receipt-content">
          <div class="receipt-section">
            <div class="receipt-title">IMS Store</div>
            <div class="receipt-subtitle">Inventory Management System</div>
          </div>

          <div class="receipt-divider"></div>

          <div class="receipt-section">
            <div class="receipt-row">
              <span class="receipt-label">Invoice #:</span>
              <span class="receipt-value">{{ viewingSale.id }}</span>
            </div>
            <div class="receipt-row">
              <span class="receipt-label">Date:</span>
              <span class="receipt-value">{{ viewingSale.date }}</span>
            </div>
          </div>

          <div class="receipt-divider"></div>

          <div class="receipt-items">
            <div class="receipt-item-row header">
              <span class="col-desc">Description</span>
              <span class="col-qty">Qty</span>
              <span class="col-price">Price</span>
              <span class="col-total">Total</span>
            </div>
            <div class="receipt-item-row">
              <span class="col-desc">{{ viewingSale.item }}</span>
              <span class="col-qty">{{ viewingSale.quantity }}</span>
              <span class="col-price">${{ viewingSale.price.toFixed(2) }}</span>
              <span class="col-total"
                >${{ (viewingSale.quantity * viewingSale.price).toFixed(2) }}</span
              >
            </div>
          </div>

          <div class="receipt-divider"></div>

          <div class="receipt-summary">
            <div class="receipt-row subtotal">
              <span>Subtotal:</span>
              <span>${{ (viewingSale.quantity * viewingSale.price).toFixed(2) }}</span>
            </div>
            <div class="receipt-row tax">
              <span>Tax (10%):</span>
              <span>${{ (viewingSale.quantity * viewingSale.price * 0.1).toFixed(2) }}</span>
            </div>
            <div class="receipt-row total">
              <span>TOTAL:</span>
              <span>${{ (viewingSale.quantity * viewingSale.price * 1.1).toFixed(2) }}</span>
            </div>
          </div>

          <div class="receipt-divider"></div>

          <div class="receipt-footer">
            <p>Thank you for your purchase!</p>
            <p style="font-size: 12px; color: #666">
              Generated on {{ new Date().toLocaleString() }}
            </p>
          </div>
        </div>
        <div class="receipt-actions">
          <button class="btn btn-secondary" @click="printReceipt">Print Receipt</button>
          <button class="btn btn-primary" @click="showViewModal = false">Close</button>
        </div>
      </div>
    </div>

    <!-- New Sale Modal -->
    <div v-if="showModal" class="modal" @click.self="showModal = false">
      <div class="modal-content">
        <div class="modal-header">
          <h2>{{ editingSale ? 'Edit Sale' : 'New Sale' }}</h2>
          <button class="modal-close" @click="showModal = false">&times;</button>
        </div>
        <form class="modal-form" @submit.prevent="saveSale">
          <div class="form-group">
            <label>Item</label>
            <select v-model="form.item" required>
              <option value="">Select item</option>
              <option>Laptop Dell XPS 15</option>
              <option>Office Chair Pro</option>
              <option>Wireless Mouse</option>
            </select>
          </div>

          <div class="form-row">
            <div class="form-group">
              <label>Quantity</label>
              <input v-model="form.quantity" type="number" min="1" required />
            </div>
            <div class="form-group">
              <label>Price</label>
              <input v-model="form.price" type="number" step="0.01" required />
            </div>
          </div>

          <div class="modal-actions">
            <button type="button" class="btn btn-secondary" @click="showModal = false">
              Cancel
            </button>
            <button type="submit" class="btn btn-primary">
              {{ editingSale ? 'Update' : 'Add' }} Sale
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from './Sidebar.vue'

export default {
  name: 'SalesPage',
  components: {
    Sidebar,
  },
  data() {
    return {
      showModal: false,
      showViewModal: false,
      viewingSale: null,
      editingSale: null,
      searchQuery: '',
      filterDate: '',
      form: {
        item: '',
        quantity: 1,
        price: 0,
      },
      sales: [
        { id: 1001, item: 'Laptop Dell XPS 15', quantity: 2, price: 1199.5, date: 'Jan 12, 2026' },
        { id: 1002, item: 'Office Chair Pro', quantity: 5, price: 249.0, date: 'Jan 12, 2026' },
        { id: 1003, item: 'Wireless Mouse', quantity: 12, price: 29.0, date: 'Jan 11, 2026' },
        { id: 1004, item: 'USB-C Hub', quantity: 8, price: 49.0, date: 'Jan 11, 2026' },
        { id: 1005, item: 'Mechanical Keyboard', quantity: 3, price: 89.0, date: 'Jan 10, 2026' },
      ],
    }
  },
  computed: {
    filteredSales() {
      return this.sales.filter((sale) => {
        const matchesSearch = sale.item.toLowerCase().includes(this.searchQuery.toLowerCase())
        return matchesSearch
      })
    },
  },
  methods: {
    viewSale(sale) {
      this.viewingSale = sale
      this.showViewModal = true
    },
    editSale(sale) {
      this.editingSale = sale
      this.form = { ...sale }
      this.showModal = true
    },
    deleteSale(sale) {
      if (confirm(`Are you sure you want to delete Sale #${sale.id}?`)) {
        this.sales = this.sales.filter((s) => s.id !== sale.id)
      }
    },
    saveSale() {
      if (this.editingSale) {
        const index = this.sales.findIndex((s) => s.id === this.editingSale.id)
        this.sales[index] = { ...this.form, id: this.editingSale.id, date: this.sales[index].date }
      } else {
        const newSale = {
          id: Math.max(...this.sales.map((s) => s.id)) + 1,
          item: this.form.item,
          quantity: parseInt(this.form.quantity),
          price: parseFloat(this.form.price),
          date: new Date().toLocaleDateString('en-US', {
            month: 'short',
            day: 'numeric',
            year: 'numeric',
          }),
        }
        this.sales.unshift(newSale)
      }
      this.showModal = false
      this.resetForm()
    },
    resetForm() {
      this.form = {
        item: '',
        quantity: 1,
        price: 0,
      }
      this.editingSale = null
    },
    printReceipt() {
      window.print()
    },
  },
}
</script>

<style>
/* Component specific styles will use Vue CSS variables */
</style>
