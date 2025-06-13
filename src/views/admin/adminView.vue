<template>
  <div class="d-flex min-vh-100">
    <!-- Sidebar -->
    <div class="bg-dark text-white d-flex flex-column p-3" style="width: 250px;">
      <h2 class="h4 text-center mb-4">Admin Dashboard</h2>
      <nav class="nav flex-column">
        <button @click="setActiveSection('login')" class="nav-link text-white" :class="{ 'active bg-primary': activeSection === 'login' }" :disabled="isAuthenticated">Login</button>
        <button @click="setActiveSection('addAdmin')" class="nav-link text-white" :class="{ 'active bg-primary': activeSection === 'addAdmin' }" :disabled="!isAuthenticated">Add Admin</button>
        <button @click="setActiveSection('getLabs')" class="nav-link text-white" :class="{ 'active bg-primary': activeSection === 'getLabs' }" :disabled="!isAuthenticated">Get Labs</button>
        <button @click="setActiveSection('registerDelivery')" class="nav-link text-white" :class="{ 'active bg-primary': activeSection === 'registerDelivery' }" :disabled="!isAuthenticated">Register Delivery</button>
        <button v-if="isAuthenticated" @click="logout" class="nav-link text-white bg-danger mt-2">Logout</button>
      </nav>
    </div>

    <!-- Main Content -->
    <div class="flex-grow-1 p-4">
      <div class="container">
        <div class="card p-4">
          <!-- Login Form -->
          <div v-if="activeSection === 'login' && !isAuthenticated">
            <h3 class="card-title mb-4">Admin Login</h3>
            <form @submit.prevent="loginAdmin">
              <div class="mb-3">
                <label class="form-label">Username</label>
                <input v-model="loginData.username" type="text" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Password</label>
                <input v-model="loginData.password" type="password" class="form-control" required />
              </div>
              <button type="submit" class="btn btn-primary">Login</button>
            </form>
          </div>

          <!-- Add Admin Form -->
          <div v-if="activeSection === 'addAdmin' && isAuthenticated">
            <h3 class="card-title mb-4">Add New Admin</h3>
            <form @submit.prevent="addAdmin">
              <div class="mb-3">
                <label class="form-label">Username</label>
                <input v-model="addAdminData.username" type="text" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Password</label>
                <input v-model="addAdminData.password" type="password" class="form-control" required />
              </div>
              <button type="submit" class="btn btn-primary">Add Admin</button>
            </form>
          </div>

          <!-- Get Labs -->
          <div v-if="activeSection === 'getLabs' && isAuthenticated">
            <h3 class="card-title mb-4">Get All Labs</h3>
            <button @click="getAllLabs" class="btn btn-primary mb-3">Fetch Labs</button>
            <div v-if="labs.length" class="mt-3">
              <h4 class="h5">Labs</h4>
              <ul class="list-group">
                <li v-for="lab in labs" :key="lab._id" class="list-group-item">
                  {{ lab.name }} (ID: {{ lab._id }})
                  <ul v-if="lab.orders.length" class="list-group list-group-flush ms-3">
                    <li v-for="order in lab.orders" :key="order._id" class="list-group-item">Order ID: {{ order._id }}</li>
                  </ul>
                </li>
              </ul>
            </div>
          </div>

          <!-- Register Delivery Form -->
          <div v-if="activeSection === 'registerDelivery' && isAuthenticated">
            <h3 class="card-title mb-4">Register Delivery Person</h3>
            <form @submit.prevent="registerDelivery">
              <div class="mb-3">
                <label class="form-label">Username</label>
                <input v-model="deliveryData.username" type="text" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Phone Number</label>
                <input v-model="deliveryData.phoneNumber" type="text" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Email</label>
                <input v-model="deliveryData.email" type="email" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Building Number</label>
                <input v-model="deliveryData.buildNo" type="text" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Floor Number</label>
                <input v-model="deliveryData.floorNo" type="text" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Address</label>
                <input v-model="deliveryData.address" type="text" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Password</label>
                <input v-model="deliveryData.password" type="password" class="form-control" required />
              </div>
              <button type="submit" class="btn btn-primary">Register</button>
            </form>
          </div>

          <!-- Response Messages -->
          <div v-if="responseMessage" class="mt-3 alert" :class="responseStatus === 200 ? 'alert-success' : 'alert-danger'">
            {{ responseMessage }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'AdminDashboard',
  data() {
    return {
      activeSection: 'login',
      isAuthenticated: !!localStorage.getItem('adminToken'),
      loginData: { username: '', password: '' },
      addAdminData: { username: '', password: '' },
      deliveryData: {
        username: '',
        phoneNumber: '',
        email: '',
        buildNo: '',
        floorNo: '',
        address: '',
        password: ''
      },
      labs: [],
      responseMessage: '',
      responseStatus: null
    };
  },
  methods: {
    setActiveSection(section) {
      this.activeSection = section;
      this.responseMessage = '';
      this.responseStatus = null;
      if (section === 'getLabs') this.getAllLabs();
    },
    async loginAdmin() {
      try {
        const response = await axios.post('http://localhost:3000/api/admin/login', this.loginData);
        localStorage.setItem('adminToken', response.data.data.token);
        this.isAuthenticated = true;
        this.responseMessage = response.data.message;
        this.responseStatus = response.data.status;
        this.loginData = { username: '', password: '' };
        this.activeSection = 'getLabs';
      } catch (error) {
        this.responseMessage = error.response?.data?.error || 'Login failed';
        this.responseStatus = error.response?.status || 500;
      }
    },
    async addAdmin() {
      try {
        const response = await axios.post('http://localhost:3000/api/admin/add-admin', this.addAdminData, {
          headers: { Authorization: `Bearer ${localStorage.getItem('adminToken')}` }
        });
        this.responseMessage = response.data.message;
        this.responseStatus = response.data.status;
        this.addAdminData = { username: '', password: '' };
      } catch (error) {
        this.responseMessage = error.response?.data?.error || 'Failed to add admin';
        this.responseStatus = error.response?.status || 500;
      }
    },
    async getAllLabs() {
      try {
        const response = await axios.get('http://localhost:3000/api/admin/labs', {
          headers: { Authorization: `Bearer ${localStorage.getItem('adminToken')}` }
        });
        this.labs = response.data.data;
        this.responseMessage = response.data.message;
        this.responseStatus = response.data.status;
      } catch (error) {
        this.responseMessage = error.response?.data?.error || 'Failed to fetch labs';
        this.responseStatus = error.response?.status || 500;
      }
    },
    async registerDelivery() {
      try {
        const response = await axios.post('http://localhost:3000/api/admin/register-delivery', this.deliveryData, {
          headers: { Authorization: `Bearer ${localStorage.getItem('adminToken')}` }
        });
        this.responseMessage = response.data.message;
        this.responseStatus = response.data.status;
        this.deliveryData = {
          username: '',
          phoneNumber: '',
          email: '',
          buildNo: '',
          floorNo: '',
          address: '',
          password: ''
        };
      } catch (error) {
        this.responseMessage = error.response?.data?.error || 'Failed to register delivery person';
        this.responseStatus = error.response?.status || 500;
      }
    },
    logout() {
      localStorage.removeItem('adminToken');
      this.isAuthenticated = false;
      this.activeSection = 'login';
      this.labs = [];
      this.responseMessage = 'Logged out successfully';
      this.responseStatus = 200;
    }
  }
};
</script>

<style scoped>
/* Bootstrap handles most styling, but we can add custom styles if needed */
.bg-dark {
  background-color: #343a40 !important;
}
.nav-link:hover {
  background-color: #495057;
}
</style>