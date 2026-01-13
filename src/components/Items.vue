<template>
  <div class="app-container">
    <Sidebar currentPage="items" @navigate="$emit('navigate', $event)" @logout="$emit('logout')" />

    <main class="main-content">
      <header class="top-bar">
        <div class="top-bar-left">
          <h1>Items Management</h1>
          <p class="breadcrumb">Manage your inventory items</p>
        </div>
        <div class="top-bar-right">
          <button class="btn btn-primary" @click="showModal = true">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <line x1="12" y1="5" x2="12" y2="19"></line>
              <line x1="5" y1="12" x2="19" y2="12"></line>
            </svg>
            Add Item
          </button>
        </div>
      </header>

      <div class="content">
        <!-- Filters -->
        <div class="filter-bar">
          <div class="search-box">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <circle cx="11" cy="11" r="8"></circle>
              <path d="m21 21-4.35-4.35"></path>
            </svg>
            <input v-model="searchQuery" type="text" placeholder="Search items...">
          </div>
          
          <select v-model="selectedCategory" class="filter-select">
            <option value="">All Categories</option>
            <option value="Electronics">Electronics</option>
            <option value="Office Supplies">Office Supplies</option>
            <option value="Furniture">Furniture</option>
          </select>
          
          <select v-model="selectedStatus" class="filter-select">
            <option value="">All Status</option>
            <option value="available">Available</option>
            <option value="low-stock">Low Stock</option>
            <option value="out-of-stock">Out of Stock</option>
          </select>
        </div>

        <!-- Items Grid -->
        <div class="items-grid">
          <div v-for="item in filteredItems" :key="item.id" class="item-card">
            <div class="item-image">
              <img :src="item.image" :alt="item.name">
              <span :class="['item-badge', item.status]">{{ formatStatus(item.status) }}</span>
            </div>
            <div class="item-content">
              <h3>{{ item.name }}</h3>
              <p class="item-category">{{ item.category }}</p>
              <p class="item-description">{{ item.description }}</p>
              <div class="item-footer">
                <span class="item-price">${{ item.price.toFixed(2) }}</span>
                <span :class="['item-stock', { warning: item.stock < 10, danger: item.stock === 0 }]">
                  Stock: {{ item.stock }}
                </span>
              </div>
              <div class="item-actions">
                <button class="btn-icon" title="Edit" @click="editItem(item)">
                  <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path>
                    <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path>
                  </svg>
                </button>
                <button class="btn-icon" title="View" @click="viewItem(item)">
                  <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                    <circle cx="12" cy="12" r="3"></circle>
                  </svg>
                </button>
                <button class="btn-icon danger" title="Delete" @click="deleteItem(item)">
                  <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <polyline points="3 6 5 6 21 6"></polyline>
                    <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- Add/Edit Item Modal -->
    <div v-if="showModal" class="modal" @click.self="showModal = false">
      <div class="modal-content">
        <div class="modal-header">
          <h2>{{ editingItem ? 'Edit Item' : 'Add New Item' }}</h2>
          <button class="modal-close" @click="showModal = false">&times;</button>
        </div>
        <form class="modal-form" @submit.prevent="saveItem">
          <div class="form-row">
            <div class="form-group">
              <label>Item Name</label>
              <input v-model="form.name" type="text" placeholder="Enter item name" required>
            </div>
            <div class="form-group">
              <label>Category</label>
              <select v-model="form.category" required>
                <option value="">Select category</option>
                <option>Electronics</option>
                <option>Furniture</option>
                <option>Office Supplies</option>
              </select>
            </div>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label>Supplier</label>
              <select v-model="form.supplier" required>
                <option value="">Select supplier</option>
                <option>Tech Corp</option>
                <option>Office Plus</option>
                <option>Furniture World</option>
              </select>
            </div>
            <div class="form-group">
              <label>Price</label>
              <input v-model="form.price" type="number" step="0.01" placeholder="0.00" required>
            </div>
          </div>
          
          <div class="form-group">
            <label>Description</label>
            <textarea v-model="form.description" rows="3" placeholder="Enter item description"></textarea>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label>Initial Stock</label>
              <input v-model="form.stock" type="number" placeholder="0" required>
            </div>
            <div class="form-group">
              <label>Expiry Date (Optional)</label>
              <input v-model="form.expiryDate" type="date">
            </div>
          </div>
          
          <div class="modal-actions">
            <button type="button" class="btn btn-secondary" @click="showModal = false">Cancel</button>
            <button type="submit" class="btn btn-primary">{{ editingItem ? 'Update' : 'Add' }} Item</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from './Sidebar.vue'

export default {
  name: 'Items',
  components: {
    Sidebar
  },
  data() {
    return {
      showModal: false,
      editingItem: null,
      searchQuery: '',
      selectedCategory: '',
      selectedStatus: '',
      form: {
        name: '',
        category: '',
        supplier: '',
        price: 0,
        description: '',
        stock: 0,
        expiryDate: ''
      },
      items: [
        {
          id: 1,
          name: 'Dell XPS 15',
          category: 'Electronics',
          description: '15.6" FHD, Intel i7, 16GB RAM, 512GB SSD',
          price: 1299.00,
          stock: 24,
          status: 'available',
          image: 'https://via.placeholder.com/200x150/6366F1/ffffff?text=Laptop'
        },
        {
          id: 2,
          name: 'Office Chair Pro',
          category: 'Furniture',
          description: 'Ergonomic design with lumbar support',
          price: 249.00,
          stock: 45,
          status: 'available',
          image: 'https://via.placeholder.com/200x150/10B981/ffffff?text=Chair'
        },
        {
          id: 3,
          name: 'Wireless Mouse',
          category: 'Electronics',
          description: '2.4GHz wireless, ergonomic design',
          price: 29.00,
          stock: 5,
          status: 'low-stock',
          image: 'https://via.placeholder.com/200x150/F59E0B/ffffff?text=Mouse'
        },
        {
          id: 4,
          name: 'Mechanical Keyboard',
          category: 'Electronics',
          description: 'RGB backlit, Cherry MX switches',
          price: 89.00,
          stock: 32,
          status: 'available',
          image: 'https://via.placeholder.com/200x150/8B5CF6/ffffff?text=Keyboard'
        },
        {
          id: 5,
          name: '27" 4K Monitor',
          category: 'Electronics',
          description: '4K UHD, IPS panel, HDR support',
          price: 449.00,
          stock: 0,
          status: 'out-of-stock',
          image: 'https://via.placeholder.com/200x150/EF4444/ffffff?text=Monitor'
        },
        {
          id: 6,
          name: 'Standing Desk',
          category: 'Furniture',
          description: 'Electric height adjustable, 60x30"',
          price: 599.00,
          stock: 18,
          status: 'available',
          image: 'https://via.placeholder.com/200x150/06B6D4/ffffff?text=Desk'
        }
      ]
    }
  },
  computed: {
    filteredItems() {
      return this.items.filter(item => {
        const matchesSearch = item.name.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
                            item.description.toLowerCase().includes(this.searchQuery.toLowerCase())
        const matchesCategory = !this.selectedCategory || item.category === this.selectedCategory
        const matchesStatus = !this.selectedStatus || item.status === this.selectedStatus
        
        return matchesSearch && matchesCategory && matchesStatus
      })
    }
  },
  methods: {
    formatStatus(status) {
      return status.split('-').map(word => word.charAt(0).toUpperCase() + word.slice(1)).join(' ')
    },
    editItem(item) {
      this.editingItem = item
      this.form = { ...item }
      this.showModal = true
    },
    viewItem(item) {
      alert(`Viewing ${item.name}`)
    },
    deleteItem(item) {
      if (confirm(`Are you sure you want to delete ${item.name}?`)) {
        this.items = this.items.filter(i => i.id !== item.id)
      }
    },
    saveItem() {
      if (this.editingItem) {
        const index = this.items.findIndex(i => i.id === this.editingItem.id)
        this.items[index] = { ...this.form, id: this.editingItem.id }
      } else {
        const newItem = {
          ...this.form,
          id: Math.max(...this.items.map(i => i.id)) + 1,
          status: this.form.stock > 10 ? 'available' : this.form.stock > 0 ? 'low-stock' : 'out-of-stock',
          image: `https://via.placeholder.com/200x150/6366F1/ffffff?text=${encodeURIComponent(this.form.name)}`
        }
        this.items.push(newItem)
      }
      this.showModal = false
      this.resetForm()
    },
    resetForm() {
      this.form = {
        name: '',
        category: '',
        supplier: '',
        price: 0,
        description: '',
        stock: 0,
        expiryDate: ''
      }
      this.editingItem = null
    }
  }
}
</script>

<style>
/* Component specific styles will use Vue CSS variables */
</style>
