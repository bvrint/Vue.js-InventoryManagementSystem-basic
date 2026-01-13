<template>
  <div class="auth-container">
    <div class="auth-card">
      <h1 class="auth-title">{{ isSignUp ? 'Sign Up' : 'Sign In' }}</h1>
      
      <form @submit.prevent="handleSubmit" class="auth-form">
        <!-- Sign Up Only Fields -->
        <div v-if="isSignUp" class="form-group">
          <label for="fullname">Full Name</label>
          <input
            id="fullname"
            v-model="form.fullname"
            type="text"
            placeholder="Enter your full name"
            required
          />
        </div>

        <!-- Common Fields -->
        <div class="form-group">
          <label for="email">Email</label>
          <input
            id="email"
            v-model="form.email"
            type="email"
            placeholder="Enter your email"
            required
          />
        </div>

        <div class="form-group">
          <label for="password">Password</label>
          <input
            id="password"
            v-model="form.password"
            type="password"
            placeholder="Enter your password"
            required
          />
        </div>

        <!-- Sign Up Only Field -->
        <div v-if="isSignUp" class="form-group">
          <label for="confirm-password">Confirm Password</label>
          <input
            id="confirm-password"
            v-model="form.confirmPassword"
            type="password"
            placeholder="Confirm your password"
            required
          />
        </div>

        <!-- Error Message -->
        <div v-if="errorMessage" class="error-message">
          {{ errorMessage }}
        </div>

        <!-- Submit Button -->
        <button type="submit" class="submit-btn">
          {{ isSignUp ? 'Create Account' : 'Sign In' }}
        </button>
      </form>

      <!-- Toggle Between Sign In and Sign Up -->
      <div class="auth-toggle">
        <p>
          {{ isSignUp ? 'Already have an account?' : "Don't have an account?" }}
          <button
            type="button"
            @click="isSignUp = !isSignUp"
            class="toggle-btn"
          >
            {{ isSignUp ? 'Sign In' : 'Sign Up' }}
          </button>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SignIn',
  data() {
    return {
      isSignUp: false,
      form: {
        fullname: '',
        email: '',
        password: '',
        confirmPassword: ''
      },
      errorMessage: ''
    }
  },
  methods: {
    handleSubmit() {
      this.errorMessage = ''

      // Validate inputs
      if (!this.form.email || !this.form.password) {
        this.errorMessage = 'Please fill in all required fields'
        return
      }

      if (this.isSignUp) {
        // Sign Up validation
        if (!this.form.fullname) {
          this.errorMessage = 'Full name is required'
          return
        }

        if (this.form.password !== this.form.confirmPassword) {
          this.errorMessage = 'Passwords do not match'
          return
        }

        if (this.form.password.length < 6) {
          this.errorMessage = 'Password must be at least 6 characters'
          return
        }

        // Sign up logic
        console.log('Sign Up:', {
          fullname: this.form.fullname,
          email: this.form.email,
          password: this.form.password
        })
        // Emit login success to navigate to dashboard
        this.$emit('login-success')
      } else {
        // Sign in logic
        console.log('Sign In:', {
          email: this.form.email,
          password: this.form.password
        })
        // Emit login success to navigate to dashboard
        this.$emit('login-success')
      }

      // Reset form
      this.resetForm()
    },
    resetForm() {
      this.form = {
        fullname: '',
        email: '',
        password: '',
        confirmPassword: ''
      }
    }
  }
}
</script>

<style scoped>
.auth-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: linear-gradient(135deg, hsla(160, 100%, 37%, 1) 0%, hsla(160, 100%, 30%, 1) 100%);
  padding: 20px;
  font-family: var(--font-family, 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif);
}

.auth-card {
  background: white;
  border-radius: var(--radius-xl, 1rem);
  box-shadow: var(--shadow-xl, 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04));
  padding: 2rem;
  width: 100%;
  max-width: 420px;
  animation: fadeInUp 0.5s ease-out;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.auth-title {
  text-align: center;
  color: var(--gray-900, #111827);
  margin-bottom: 1.5rem;
  font-size: var(--font-size-2xl, 1.5rem);
  font-weight: 700;
}

.auth-form {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-md, 1rem);
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-xs, 0.5rem);
}

.form-group label {
  font-weight: 500;
  color: var(--gray-700, #374151);
  font-size: var(--font-size-sm, 0.875rem);
}

.form-group input {
  padding: 0.75rem 1rem;
  border: 1px solid var(--gray-300, #D1D5DB);
  border-radius: var(--radius, 0.5rem);
  font-size: var(--font-size-base, 1rem);
  font-family: var(--font-family, inherit);
  background: white;
  color: var(--gray-900, #111827);
  transition: var(--transition, all 0.2s ease-in-out);
}

.form-group input:focus {
  outline: none;
  border-color: var(--primary, hsla(160, 100%, 37%, 1));
  box-shadow: 0 0 0 3px hsla(160, 100%, 37%, 0.1);
}

.form-group input::placeholder {
  color: var(--gray-400, #9CA3AF);
}

.error-message {
  color: var(--danger, #EF4444);
  font-size: var(--font-size-sm, 0.875rem);
  padding: 10px;
  background-color: rgba(239, 68, 68, 0.1);
  border-radius: var(--radius, 0.5rem);
  text-align: center;
}

.submit-btn {
  padding: 0.625rem 1.25rem;
  background: var(--primary, hsla(160, 100%, 37%, 1));
  color: white;
  border: none;
  border-radius: var(--radius, 0.5rem);
  font-size: var(--font-size-base, 1rem);
  font-weight: 600;
  cursor: pointer;
  transition: var(--transition, all 0.2s ease-in-out);
}

.submit-btn:hover {
  background: var(--primary-dark, hsla(160, 100%, 30%, 1));
  transform: translateY(-1px);
  box-shadow: var(--shadow-md, 0 4px 6px -1px rgba(0, 0, 0, 0.1));
}

.submit-btn:active {
  transform: translateY(0);
}

.auth-toggle {
  text-align: center;
  margin-top: var(--spacing-md, 1rem);
  padding-top: var(--spacing-md, 1rem);
  border-top: 1px solid var(--gray-200, #E5E7EB);
}

.auth-toggle p {
  color: var(--gray-600, #4B5563);
  font-size: var(--font-size-sm, 0.875rem);
}

.toggle-btn {
  background: none;
  border: none;
  color: var(--primary, hsla(160, 100%, 37%, 1));
  font-weight: 600;
  cursor: pointer;
  font-size: var(--font-size-sm, 0.875rem);
  margin-left: 5px;
  transition: var(--transition, all 0.2s ease-in-out);
}

.toggle-btn:hover {
  color: var(--primary-dark, hsla(160, 100%, 30%, 1));
  text-decoration: underline;
}
</style>
