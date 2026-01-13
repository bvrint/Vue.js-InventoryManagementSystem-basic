<template>
  <div class="app-container">
    <Sidebar currentPage="dashboard" @navigate="$emit('navigate', $event)" @logout="$emit('logout')" />

    <main class="main-content">
      <header class="top-bar">
        <div class="top-bar-left">
          <h1>Dashboard</h1>
          <p class="breadcrumb">Overview of your inventory</p>
        </div>
        <div class="top-bar-right">
          <div class="user-menu">
            <div class="user-avatar">A</div>
            <div class="user-info">
              <span class="user-name">Admin User</span>
              <span class="user-role">Administrator</span>
            </div>
          </div>
        </div>
      </header>

      <div class="content">
        <!-- Stats Cards -->
        <div class="stats-grid">
          <div class="stat-card">
            <div class="stat-icon blue">
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"></path>
              </svg>
            </div>
            <div class="stat-content">
              <h3>Total Items</h3>
              <p class="stat-number">{{ stats.totalItems }}</p>
              <span class="stat-change positive">+12% from last month</span>
            </div>
          </div>

          <div class="stat-card">
            <div class="stat-icon green">
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <line x1="12" y1="1" x2="12" y2="23"></line>
                <path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"></path>
              </svg>
            </div>
            <div class="stat-content">
              <h3>Total Sales</h3>
              <p class="stat-number">${{ stats.totalSales }}</p>
              <span class="stat-change positive">+8% from last month</span>
            </div>
          </div>

          <div class="stat-card">
            <div class="stat-icon purple">
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M4 4h6v6H4zM14 4h6v6h-6zM4 14h6v6H4zM14 14h6v6h-6z"></path>
              </svg>
            </div>
            <div class="stat-content">
              <h3>Categories</h3>
              <p class="stat-number">{{ stats.categories }}</p>
              <span class="stat-change neutral">No change</span>
            </div>
          </div>

          <div class="stat-card">
            <div class="stat-icon orange">
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M22 12h-4l-3 9L9 3l-3 9H2"></path>
              </svg>
            </div>
            <div class="stat-content">
              <h3>Low Stock Items</h3>
              <p class="stat-number">{{ stats.lowStock }}</p>
              <span class="stat-change negative">Needs attention</span>
            </div>
          </div>
        </div>

        <!-- Recent Activity Section -->
        <div class="dashboard-grid">
          <div class="card">
            <div class="card-header">
              <h2>Recent Sales</h2>
              <a href="#" class="link">View All</a>
            </div>
            <div class="table-container">
              <table class="data-table">
                <thead>
                  <tr>
                    <th>Item</th>
                    <th>Quantity</th>
                    <th>Price</th>
                    <th>Date</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="sale in recentSales" :key="sale.id">
                    <td>{{ sale.item }}</td>
                    <td>{{ sale.quantity }}</td>
                    <td>${{ sale.price.toFixed(2) }}</td>
                    <td>{{ sale.date }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <div class="card">
            <div class="card-header">
              <h2>Stock Alerts</h2>
              <a href="#" class="link">View All</a>
            </div>
            <div class="alert-list">
              <div v-for="alert in stockAlerts" :key="alert.id" :class="['alert-item', alert.type]">
                <div class="alert-icon">
                  <svg v-if="alert.type === 'warning'" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path>
                    <line x1="12" y1="9" x2="12" y2="13"></line>
                    <line x1="12" y1="17" x2="12.01" y2="17"></line>
                  </svg>
                  <svg v-else-if="alert.type === 'danger'" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <circle cx="12" cy="12" r="10"></circle>
                    <line x1="12" y1="8" x2="12" y2="12"></line>
                    <line x1="12" y1="16" x2="12.01" y2="16"></line>
                  </svg>
                  <svg v-else-if="alert.type === 'success'" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <polyline points="20 6 9 17 4 12"></polyline>
                  </svg>
                </div>
                <div class="alert-content">
                  <h4>{{ alert.title }}</h4>
                  <p>{{ alert.message }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import Sidebar from './Sidebar.vue'

export default {
  name: 'Dashboard',
  components: {
    Sidebar
  },
  data() {
    return {
      stats: {
        totalItems: 1248,
        totalSales: '45,692',
        categories: 24,
        lowStock: 18
      },
      recentSales: [
        { id: 1, item: 'Laptop Dell XPS 15', quantity: 2, price: 2399.00, date: 'Jan 12, 2026' },
        { id: 2, item: 'Office Chair Pro', quantity: 5, price: 1245.00, date: 'Jan 12, 2026' },
        { id: 3, item: 'Wireless Mouse', quantity: 12, price: 348.00, date: 'Jan 11, 2026' },
        { id: 4, item: 'USB-C Hub', quantity: 8, price: 392.00, date: 'Jan 11, 2026' }
      ],
      stockAlerts: [
        { id: 1, type: 'warning', title: 'Low Stock: Wireless Keyboard', message: 'Only 5 units remaining' },
        { id: 2, type: 'warning', title: 'Low Stock: Monitor Stand', message: 'Only 3 units remaining' },
        { id: 3, type: 'danger', title: 'Expiring Soon: Printer Ink', message: 'Expires on Jan 20, 2026' },
        { id: 4, type: 'success', title: 'Stock Replenished: USB Cables', message: '50 units added to inventory' }
      ]
    }
  }
}
</script>

<style>
/* Component specific styles will use Vue CSS variables */
</style>
