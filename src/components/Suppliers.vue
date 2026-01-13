<template>
  <div class="app-container">
    <Sidebar currentPage="suppliers" @navigate="$emit('navigate', $event)" @logout="$emit('logout')" />

    <main class="main-content">
      <header class="top-bar">
        <div class="top-bar-left">
          <h1>Suppliers</h1>
          <p class="breadcrumb">Manage supplier information</p>
        </div>
        <div class="top-bar-right">
          <button class="btn btn-primary" @click="showModal = true">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <line x1="12" y1="5" x2="12" y2="19"></line>
              <line x1="5" y1="12" x2="19" y2="12"></line>
            </svg>
            Add Supplier
          </button>
        </div>
      </header>

      <div class="content">
        <!-- Filter Bar -->
        <div class="filter-bar">
          <div class="search-box">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <circle cx="11" cy="11" r="8"></circle>
              <path d="m21 21-4.35-4.35"></path>
            </svg>
            <input v-model="searchQuery" type="text" placeholder="Search suppliers...">
          </div>
        </div>

        <!-- Suppliers Grid -->
        <div class="categories-grid">
          <div v-for="supplier in filteredSuppliers" :key="supplier.id" class="category-card">
            <div :class="['category-icon', supplier.color]">
              <svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M16 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path>
                <circle cx="8.5" cy="7" r="4"></circle>
                <line x1="20" y1="8" x2="20" y2="14"></line>
                <line x1="23" y1="11" x2="17" y2="11"></line>
              </svg>
            </div>
            <h3>{{ supplier.name }}</h3>
            <p>{{ supplier.contact }}</p>
            <p class="item-category">{{ supplier.email }}</p>
            <div class="category-stats">
              <span>{{ supplier.itemsSupplied }} items supplied</span>
            </div>
            <div class="category-actions">
              <button class="btn-icon" @click="editSupplier(supplier)">
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path>
                  <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path>
                </svg>
              </button>
              <button class="btn-icon danger" @click="deleteSupplier(supplier)">
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <polyline points="3 6 5 6 21 6"></polyline>
                  <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- Add/Edit Supplier Modal -->
    <div v-if="showModal" class="modal" @click.self="showModal = false">
      <div class="modal-content modal-small">
        <div class="modal-header">
          <h2>{{ editingSupplier ? 'Edit Supplier' : 'Add Supplier' }}</h2>
          <button class="modal-close" @click="showModal = false">&times;</button>
        </div>
        <form class="modal-form" @submit.prevent="saveSupplier">
          <div class="form-group">
            <label>Supplier Name</label>
            <input v-model="form.name" type="text" placeholder="Enter supplier name" required>
          </div>

          <div class="form-group">
            <label>Contact Person</label>
            <input v-model="form.contact" type="text" placeholder="Enter contact person" required>
          </div>

          <div class="form-group">
            <label>Email</label>
            <input v-model="form.email" type="email" placeholder="Enter email" required>
          </div>

          <div class="form-group">
            <label>Phone</label>
            <input v-model="form.phone" type="tel" placeholder="Enter phone number">
          </div>

          <div class="form-group">
            <label>Address</label>
            <textarea v-model="form.address" rows="3" placeholder="Enter address"></textarea>
          </div>

          <div class="modal-actions">
            <button type="button" class="btn btn-secondary" @click="showModal = false">Cancel</button>
            <button type="submit" class="btn btn-primary">
              {{ editingSupplier ? 'Update' : 'Add' }} Supplier
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
  name: 'SuppliersPage',
  components: {
    Sidebar
  },
  data() {
    return {
      showModal: false,
      editingSupplier: null,
      searchQuery: '',
      form: {
        name: '',
        contact: '',
        email: '',
        phone: '',
        address: ''
      },
      suppliers: [
        { id: 1, name: 'Tech Corp', contact: 'John Smith', email: 'john@techcorp.com', phone: '555-0101', itemsSupplied: 45, color: 'blue' },
        { id: 2, name: 'Office Plus', contact: 'Sarah Johnson', email: 'sarah@officeplus.com', phone: '555-0102', itemsSupplied: 32, color: 'green' },
        { id: 3, name: 'Furniture World', contact: 'Mike Davis', email: 'mike@furnitureworld.com', phone: '555-0103', itemsSupplied: 28, color: 'purple' }
      ]
    }
  },
  computed: {
    filteredSuppliers() {
      return this.suppliers.filter(supplier => {
        return supplier.name.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
               supplier.contact.toLowerCase().includes(this.searchQuery.toLowerCase())
      })
    }
  },
  methods: {
    editSupplier(supplier) {
      this.editingSupplier = supplier
      this.form = { ...supplier }
      this.showModal = true
    },
    deleteSupplier(supplier) {
      if (confirm(`Are you sure you want to delete ${supplier.name}?`)) {
        this.suppliers = this.suppliers.filter(s => s.id !== supplier.id)
      }
    },
    saveSupplier() {
      if (this.editingSupplier) {
        const index = this.suppliers.findIndex(s => s.id === this.editingSupplier.id)
        this.suppliers[index] = { ...this.form, id: this.editingSupplier.id }
      } else {
        const newSupplier = {
          ...this.form,
          id: Math.max(...this.suppliers.map(s => s.id)) + 1,
          itemsSupplied: 0,
          color: 'blue'
        }
        this.suppliers.push(newSupplier)
      }
      this.showModal = false
      this.resetForm()
    },
    resetForm() {
      this.form = {
        name: '',
        contact: '',
        email: '',
        phone: '',
        address: ''
      }
      this.editingSupplier = null
    }
  }
}
</script>

<style>
/* Component specific styles will use Vue CSS variables */
</style>
