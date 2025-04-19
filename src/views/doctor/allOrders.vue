<template>
  <div>
    <nav-no-anim />
    <div class="content">
      <h2>Orders</h2>

      <div class="controls">
        <div class="search-box">
          <input
              type="text"
              v-model="searchTerm"
              @input="handleSearch"
              placeholder="Search orders..."
          >
          <select v-model="searchField">
            <option value="all">All Fields</option>
            <option value="UID">Order ID</option>
            <option value="patientName">Patient Name</option>
            <option value="status">Status</option>
            <option value="doctor">Doctor</option>
          </select>
        </div>

        <div class="date-filter">
          <input type="date" v-model="startDate" @change="applyFilters">
          <span>to</span>
          <input type="date" v-model="endDate" @change="applyFilters">
          <button @click="clearDateFilter">Clear Dates</button>
        </div>

        <div class="status-filters">
          <button
              v-for="filter in filters"
              :key="filter.value"
              :class="{ active: activeFilter === filter.value }"
              @click="applyStatusFilter(filter.value)"
          >
            {{ filter.label }}
          </button>
        </div>
      </div>

      <div v-if="loading" class="loading">Loading orders...</div>
      <div v-else-if="error" class="error">{{ error }}</div>

      <div v-else class="order-cards">
        <div
            v-for="order in paginatedOrders"
            :key="order._id"
            class="card"
            :class="statusClass(order.status)"
        >
          <h3>Order #{{ order.UID }}</h3>
          <p><strong>Patient Name:</strong> {{ order.patientName }}</p>
          <p><strong>Status:</strong> {{ order.status }}</p>
          <p><strong>Price:</strong> {{ order.price }}</p>
          <p><strong>Paid:</strong> {{ order.paied }}</p>
          <p><strong>rest:</strong> {{ order.rest }}</p>
          <p><strong>Last Updated:</strong> {{ formatDate(order.updatedAt) }}</p>
          <router-link
              :to="{ name: 'showOrder', params: { id: order._id } }"
              class="view-order-btn"
          >
            View Order Details
          </router-link>
        </div>
      </div>

      <div v-if="totalPages > 1" class="pagination">
        <button
            @click="currentPage--"
            :disabled="currentPage === 1"
        >
          Previous
        </button>
        <span>Page {{ currentPage }} of {{ totalPages }}</span>
        <button
            @click="currentPage++"
            :disabled="currentPage === totalPages"
        >
          Next
        </button>
      </div>
    </div>

    <div class="chat-icon">
      <a href="#chat">🗨️</a>
    </div>
  </div>
</template>

<script>
import NavNoAnim from "@/components/navbars/doctorNavbar.vue";
import axios from "axios";
import { format } from "date-fns";

export default {
  name: "AllOrders",
  components: {
    "nav-no-anim": NavNoAnim,
  },
  data() {
    return {
      baseUrl: "https://rr-5d46.onrender.com/docdash",
      orders: [],
      filteredOrders: [],
      loading: false,
      error: null,
      searchTerm: "",
      searchField: "all",
      activeFilter: "all",
      startDate: "",
      endDate: "",
      currentPage: 1,
      itemsPerPage: 10,
      cancelToken: null,
      isMounted: false,
      filters: [
        { label: "All", value: "all" },
        { label: "Underway", value: "docready" },
        { label: "Ready", value: "lab" },
        { label: "Lab Completed", value: "doctor" },
        { label: "Completed", value: "end" },
      ],
    };
  },
  computed: {
    isAuthenticated() {
      return !!localStorage.getItem("token");
    },
    totalPages() {
      return Math.ceil(this.filteredOrders.length / this.itemsPerPage);
    },
    paginatedOrders() {
      const start = (this.currentPage - 1) * this.itemsPerPage;
      return this.filteredOrders.slice(start, start + this.itemsPerPage);
    },
  },
  mounted() {
    this.isMounted = true;
    if (!this.isAuthenticated) {
      this.$router.push("/login");
      return;
    }
    this.fetchOrders();
  },
  beforeUnmount() {
    this.isMounted = false;
    if (this.cancelToken) {
      this.cancelToken.cancel("Component unmounted");
    }
  },
  methods: {
    formatDate(date) {
      return date ? format(new Date(date), "MMM dd, yyyy HH:mm") : "";
    },
    statusClass(status) {
      return `status-${status.toLowerCase().replace(/[()]/g, '').replace(/\s+/g, '-')}`;
    },
    async fetchOrders() {
      if (!this.isMounted) return;

      this.loading = true;
      this.error = null;

      // Cancel previous request if it exists
      if (this.cancelToken) {
        this.cancelToken.cancel("New request initiated");
      }

      this.cancelToken = axios.CancelToken.source();
      const token = localStorage.getItem("token");

      try {
        const { data } = await axios.get(`${this.baseUrl}/doctor`, {
          headers: { Authorization: `Bearer ${token}` },
          cancelToken: this.cancelToken.token
        });
        console.log(data);
        if (this.isMounted) {
          this.orders = data.orders || data;
          this.filteredOrders = [...this.orders];
        }
      } catch (error) {
        if (axios.isCancel(error)) {
          console.log("Request canceled:", error.message);
        } else if (this.isMounted) {
          this.handleError(error);
        }
      } finally {
        if (this.isMounted) {
          this.loading = false;
        }
      }
    },
    applyFilters() {
      let filtered = [...this.orders];

      // Apply status filter
      if (this.activeFilter !== "all") {
        filtered = filtered.filter(order => order.status.includes(this.activeFilter));
      }

      // Apply date range filter
      if (this.startDate && this.endDate) {
        const start = new Date(this.startDate);
        const end = new Date(this.endDate);
        end.setHours(23, 59, 59, 999);

        filtered = filtered.filter(order => {
          const orderDate = new Date(order.updatedAt);
          return orderDate >= start && orderDate <= end;
        });
      }

      // Apply search filter
      if (this.searchTerm.trim()) {
        const term = this.searchTerm.toLowerCase().trim();
        filtered = filtered.filter((order) => {
          if (this.searchField === 'all') {
            return (
                (order.UID?.toLowerCase().includes(term)) ||
                (order.patientName?.toLowerCase().includes(term)) ||
                (order.status?.toLowerCase().includes(term)) ||
                (order.doctor?.toLowerCase().includes(term))
            );
          }
          const fieldValue = order[this.searchField]?.toLowerCase();
          return fieldValue?.includes(term);
        });
      }

      this.filteredOrders = filtered;
      this.currentPage = 1;
    },
    clearDateFilter() {
      this.startDate = "";
      this.endDate = "";
      this.applyFilters();
    },
    applyStatusFilter(filter) {
      this.activeFilter = filter;
      this.applyFilters();
    },
    handleSearch() {
      this.applyFilters();
    },
    handleError(error) {
      let message = "An error occurred";
      if (error.response) {
        const { status, data } = error.response;
        switch (status) {
          case 400:
            message = data.message || "Invalid request";
            break;
          case 401:
            message = "Session expired, please login again";
            this.$router.push("/login");
            return;
          case 500:
            message = "Server error, please try again later";
            break;
        }
      } else {
        message = "Network error, check your internet connection";
      }

      this.error = message;
    },
  },
};
</script>

<style scoped>
/* Your existing styles remain the same */
.content {
  padding: 80px 20px 20px;
  max-width: 1200px;
  margin: 0 auto;
}

h2 {
  color: #333;
  margin-bottom: 20px;
}

.controls {
  margin-bottom: 20px;
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
}

.search-box {
  display: flex;
  gap: 10px;
}

.search-box input {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  min-width: 250px;
}

.date-filter {
  display: flex;
  align-items: center;
  gap: 10px;
}

.date-filter input {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.status-filters {
  display: flex;
  gap: 10px;
}

.status-filters button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
}

.status-filters button.active {
  background: #00c3ff;
  color: white;
  border-color: #00c3ff;
}

.order-cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
}

.card {
  background: white;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
  display: flex;
  flex-direction: column;
}

.card:hover {
  transform: translateY(-5px);
}

.card h3 {
  margin-top: 0;
  color: #00c3ff;
}

.card p {
  margin: 8px 0;
}

.view-order-btn {
  display: inline-block;
  margin-top: 15px;
  padding: 8px 16px;
  background-color: #00c3ff;
  color: white;
  text-decoration: none;
  border-radius: 4px;
  text-align: center;
  transition: background-color 0.3s;
}

.view-order-btn:hover {
  background-color: #0099cc;
}

/* Status classes */
.status-docready {
  border-left: 4px solid #ffc107;
}

.status-lab {
  border-left: 4px solid #17a2b8;
}

.status-doctor {
  border-left: 4px solid #007bff;
}

.status-end {
  border-left: 4px solid #28a745;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
  margin-top: 30px;
}

.pagination button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
}

.pagination button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.loading {
  padding: 20px;
  text-align: center;
  color: #666;
}

.error {
  padding: 20px;
  text-align: center;
  color: #ff4444;
  background-color: #ffeeee;
  border-radius: 4px;
  margin: 20px 0;
}

.chat-icon {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background: #00c3ff;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.chat-icon a {
  color: white;
  text-decoration: none;
  font-size: 20px;
}
</style>