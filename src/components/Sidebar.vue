<template>
  <aside class="sidebar">
    <div class="sidebar-header">
      <div class="logo-small">
        <svg width="36" height="36" viewBox="0 0 48 48" fill="none">
          <rect width="48" height="48" rx="12" fill="hsla(160, 100%, 37%, 1)" />
          <path
            d="M16 20h16M16 24h16M16 28h10"
            stroke="white"
            stroke-width="2.5"
            stroke-linecap="round"
          />
          <rect
            x="12"
            y="14"
            width="24"
            height="22"
            rx="2"
            stroke="white"
            stroke-width="2.5"
            fill="none"
          />
        </svg>
      </div>
      <h2>IMS</h2>
    </div>

    <nav class="sidebar-nav">
      <a
        href="#"
        @click.prevent="navigateTo('dashboard')"
        :class="['nav-item', { active: currentPage === 'dashboard' }]"
      >
        <svg
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <rect x="3" y="3" width="7" height="7"></rect>
          <rect x="14" y="3" width="7" height="7"></rect>
          <rect x="14" y="14" width="7" height="7"></rect>
          <rect x="3" y="14" width="7" height="7"></rect>
        </svg>
        <span>Dashboard</span>
      </a>

      <a
        href="#"
        @click.prevent="navigateTo('items')"
        :class="['nav-item', { active: currentPage === 'items' }]"
      >
        <svg
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <path
            d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"
          ></path>
        </svg>
        <span>Items</span>
      </a>

      <a
        href="#"
        @click.prevent="navigateTo('sales')"
        :class="['nav-item', { active: currentPage === 'sales' }]"
      >
        <svg
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <line x1="12" y1="1" x2="12" y2="23"></line>
          <path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"></path>
        </svg>
        <span>Sales</span>
      </a>

      <a
        href="#"
        @click.prevent="navigateTo('stock')"
        :class="['nav-item', { active: currentPage === 'stock' }]"
      >
        <svg
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <path
            d="M21 8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"
          ></path>
          <polyline points="7.5 4.21 12 6.81 16.5 4.21"></polyline>
          <polyline points="7.5 19.79 7.5 14.6 3 12"></polyline>
          <polyline points="21 12 16.5 14.6 16.5 19.79"></polyline>
          <polyline points="3.27 6.96 12 12.01 20.73 6.96"></polyline>
          <line x1="12" y1="22.08" x2="12" y2="12"></line>
        </svg>
        <span>Stock</span>
      </a>

      <a
        href="#"
        @click.prevent="navigateTo('categories')"
        :class="['nav-item', { active: currentPage === 'categories' }]"
      >
        <svg
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <path d="M4 4h6v6H4zM14 4h6v6h-6zM4 14h6v6H4zM14 14h6v6h-6z"></path>
        </svg>
        <span>Categories</span>
      </a>

      <a
        href="#"
        @click.prevent="navigateTo('suppliers')"
        :class="['nav-item', { active: currentPage === 'suppliers' }]"
      >
        <svg
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <path d="M16 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path>
          <circle cx="8.5" cy="7" r="4"></circle>
          <line x1="20" y1="8" x2="20" y2="14"></line>
          <line x1="23" y1="11" x2="17" y2="11"></line>
        </svg>
        <span>Suppliers</span>
      </a>
    </nav>

    <div class="sidebar-footer">
      <button class="btn-logout" @click="handleLogout">
        <svg
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"></path>
          <polyline points="16 17 21 12 16 7"></polyline>
          <line x1="21" y1="12" x2="9" y2="12"></line>
        </svg>
        <span>Logout</span>
      </button>
    </div>
  </aside>
</template>

<script>
export default {
  name: 'SidebarPage',
  props: {
    currentPage: {
      type: String,
      default: 'dashboard',
    },
  },
  methods: {
    navigateTo(page) {
      this.$emit('navigate', page)
    },
    handleLogout() {
      this.$emit('logout')
    },
  },
}
</script>

<style>
/* Component specific styles will use Vue CSS variables */
</style>
