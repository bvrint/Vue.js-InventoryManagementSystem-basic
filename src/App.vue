<script setup>
import { ref } from 'vue'
import SignIn from './components/SignIn.vue'
import Dashboard from './components/Dashboard.vue'
import Items from './components/Items.vue'
import Categories from './components/Categories.vue'
import Sales from './components/Sales.vue'
import Stock from './components/Stock.vue'
import Suppliers from './components/Suppliers.vue'

const isLoggedIn = ref(false)
const currentPage = ref('dashboard')

const handleLogin = () => {
  isLoggedIn.value = true
  currentPage.value = 'dashboard'
}

const handleLogout = () => {
  isLoggedIn.value = false
}

const handleNavigate = (page) => {
  currentPage.value = page
}
</script>

<template>
  <div v-if="!isLoggedIn">
    <SignIn @login-success="handleLogin" />
  </div>
  
  <div v-else>
    <Dashboard v-if="currentPage === 'dashboard'" @navigate="handleNavigate" @logout="handleLogout" />
    <Items v-else-if="currentPage === 'items'" @navigate="handleNavigate" @logout="handleLogout" />
    <Sales v-else-if="currentPage === 'sales'" @navigate="handleNavigate" @logout="handleLogout" />
    <Stock v-else-if="currentPage === 'stock'" @navigate="handleNavigate" @logout="handleLogout" />
    <Categories v-else-if="currentPage === 'categories'" @navigate="handleNavigate" @logout="handleLogout" />
    <Suppliers v-else-if="currentPage === 'suppliers'" @navigate="handleNavigate" @logout="handleLogout" />
    <Dashboard v-else @navigate="handleNavigate" @logout="handleLogout" />
  </div>
</template>
