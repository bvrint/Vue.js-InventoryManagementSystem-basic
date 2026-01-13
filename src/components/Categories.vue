<template>
  <div class="app-container">
    <Sidebar currentPage="categories" @navigate="$emit('navigate', $event)" @logout="$emit('logout')" />

    <main class="main-content">
      <header class="top-bar">
        <div class="top-bar-left">
          <h1>Categories</h1>
          <p class="breadcrumb">Manage product categories</p>
        </div>
        <div class="top-bar-right">
          <button class="btn btn-primary" @click="showModal = true">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <line x1="12" y1="5" x2="12" y2="19"></line>
              <line x1="5" y1="12" x2="19" y2="12"></line>
            </svg>
            Add Category
          </button>
        </div>
      </header>

      <div class="content">
        <div class="categories-grid">
          <div v-for="category in categories" :key="category.id" class="category-card">
            <div :class="['category-icon', category.color]">
              <svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" v-html="category.iconPath"></svg>
            </div>
            <h3>{{ category.name }}</h3>
            <p>{{ category.description }}</p>
            <div class="category-stats">
              <span>{{ category.itemCount }} items</span>
            </div>
            <div class="category-actions">
              <button class="btn-icon" @click="editCategory(category)">
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path>
                  <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path>
                </svg>
              </button>
              <button class="btn-icon danger" @click="deleteCategory(category)">
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

    <!-- Add/Edit Category Modal -->
    <div v-if="showModal" class="modal" @click.self="showModal = false">
      <div class="modal-content modal-small">
        <div class="modal-header">
          <h2>{{ editingCategory ? 'Edit Category' : 'Add Category' }}</h2>
          <button class="modal-close" @click="showModal = false">&times;</button>
        </div>
        <form class="modal-form" @submit.prevent="saveCategory">
          <div class="form-group">
            <label>Category Name</label>
            <input v-model="form.name" type="text" placeholder="Enter category name" required>
          </div>
          <div class="form-group">
            <label>Description</label>
            <textarea v-model="form.description" rows="3" placeholder="Enter category description"></textarea>
          </div>
          <div class="form-group">
            <label>Color</label>
            <select v-model="form.color" required>
              <option value="blue">Blue</option>
              <option value="green">Green</option>
              <option value="purple">Purple</option>
              <option value="orange">Orange</option>
              <option value="red">Red</option>
            </select>
          </div>
          <div class="modal-actions">
            <button type="button" class="btn btn-secondary" @click="showModal = false">Cancel</button>
            <button type="submit" class="btn btn-primary">
              {{ editingCategory ? 'Update' : 'Add' }} Category
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
  name: 'Categories',
  components: {
    Sidebar
  },
  data() {
    return {
      showModal: false,
      editingCategory: null,
      form: {
        name: '',
        description: '',
        color: 'blue'
      },
      categories: [
        {
          id: 1,
          name: 'Electronics',
          description: 'Computer and electronic devices',
          itemCount: 142,
          color: 'blue',
          iconPath: '<rect x="2" y="7" width="20" height="14" rx="2" ry="2"></rect><path d="M16 21V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v16"></path>'
        },
        {
          id: 2,
          name: 'Furniture',
          description: 'Office and home furniture',
          itemCount: 68,
          color: 'green',
          iconPath: '<path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path><polyline points="9 22 9 12 15 12 15 22"></polyline>'
        },
        {
          id: 3,
          name: 'Office Supplies',
          description: 'Stationery and office essentials',
          itemCount: 234,
          color: 'purple',
          iconPath: '<path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line><polyline points="10 9 9 9 8 9"></polyline>'
        }
      ]
    }
  },
  methods: {
    editCategory(category) {
      this.editingCategory = category
      this.form = { ...category }
      this.showModal = true
    },
    deleteCategory(category) {
      if (confirm(`Are you sure you want to delete ${category.name}?`)) {
        this.categories = this.categories.filter(c => c.id !== category.id)
      }
    },
    saveCategory() {
      if (this.editingCategory) {
        const index = this.categories.findIndex(c => c.id === this.editingCategory.id)
        this.categories[index] = { ...this.form, id: this.editingCategory.id, itemCount: this.categories[index].itemCount }
      } else {
        const newCategory = {
          ...this.form,
          id: Math.max(...this.categories.map(c => c.id)) + 1,
          itemCount: 0,
          iconPath: '<rect x="4" y="4" width="6" height="6"></rect><rect x="14" y="4" width="6" height="6"></rect><rect x="4" y="14" width="6" height="6"></rect><rect x="14" y="14" width="6" height="6"></rect>'
        }
        this.categories.push(newCategory)
      }
      this.showModal = false
      this.resetForm()
    },
    resetForm() {
      this.form = {
        name: '',
        description: '',
        color: 'blue'
      }
      this.editingCategory = null
    }
  }
}
</script>

<style>
/* Component specific styles will use Vue CSS variables */
</style>
