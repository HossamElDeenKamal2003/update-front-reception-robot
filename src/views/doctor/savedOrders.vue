```vue
<template>
  <div>
    <nav-no-anim />
    <div class="content">
      <h2>الطلبات المحفوظة</h2>

      <div class="controls">
        <div class="search-box">
          <input
              type="text"
              v-model="searchTerm"
              @input="handleSearch"
              placeholder="البحث في الطلبات..."
          >
          <select v-model="searchField">
            <option value="all">جميع الحقول</option>
            <option value="UID">رقم الطلب</option>
            <option value="patientName">اسم المريض</option>
            <option value="status">الحالة</option>
          </select>
        </div>

        <div class="date-filter">
          <input type="date" v-model="startDate" @change="applyFilters">
          <span>إلى</span>
          <input type="date" v-model="endDate" @change="applyFilters">
          <button @click="clearDateFilter">مسح التواريخ</button>
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

      <div v-if="loading" class="loading">جاري تحميل الطلبات...</div>
      <div v-else-if="error" class="error">{{ error }}</div>
      <div v-else-if="orderGroups.length === 0" class="no-orders">
        لا توجد طلبات محفوظة
      </div>

      <div v-else class="order-cards">
        <div
            v-for="group in paginatedGroups"
            :key="group._id"
            class="card"
        >
          <h3>مجموعة الطلبات #{{ group._id }}</h3>

          <div class="order-list">
            <router-link
                v-for="order in group.orders"
                :key="order._id"
                :to="{ name: 'showOrder', params: { id: order._id } }"
                class="order-item"
            >
              <div class="order-header">
                <span class="order-patient">المريض: {{ order.patientName }}</span>
                <span class="order-uid">رقم الطلب: #{{ order.UID }}</span>
                <span class="order-status" :class="statusClass(order.status)">
                  {{ translateStatus(order.status) }}
                </span>
              </div>
            </router-link>
          </div>

          <div class="order-actions">
            <button
                class="send-order-btn"
                @click="sendSavedOrders(group)"
                v-if="group.orders.some(order => order.save)"
            >
              إرسال الطلبات
            </button>
          </div>
        </div>
      </div>

      <div v-if="totalPages > 1" class="pagination">
        <button
            @click="currentPage--"
            :disabled="currentPage === 1"
        >
          السابق
        </button>
        <span>صفحة {{ currentPage }} من {{ totalPages }}</span>
        <button
            @click="currentPage++"
            :disabled="currentPage === totalPages"
        >
          التالي
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
  name: "savedOrders",
  components: {
    "nav-no-anim": NavNoAnim,
  },
  data() {
    return {
      baseUrl: "https://rr-5d46.onrender.com",
      orderGroups: [],
      loading: false,
      error: null,
      searchTerm: "",
      searchField: "all",
      activeFilter: "all",
      startDate: "",
      endDate: "",
      currentPage: 1,
      itemsPerPage: 5,
      cancelToken: null,
      isMounted: false,
      filters: [
        { label: "الكل", value: "all" },
        { label: "تحت الإجراء", value: "DoctorReady(p)" },
        { label: "جاهز", value: "lab" },
        { label: "مكتمل", value: "end" },
      ],
    };
  },
  computed: {
    isAuthenticated() {
      return !!localStorage.getItem("token");
    },
    totalPages() {
      return Math.ceil(this.orderGroups.length / this.itemsPerPage);
    },
    paginatedGroups() {
      const start = (this.currentPage - 1) * this.itemsPerPage;
      return this.orderGroups.slice(start, start + this.itemsPerPage);
    },
  },
  mounted() {
    this.isMounted = true;
    this.fetchOrders();
  },
  beforeUnmount() {
    this.isMounted = false;
    if (this.cancelToken) {
      this.cancelToken.cancel("Component destroyed");
    }
  },
  methods: {
    formatDate(date) {
      return date ? format(new Date(date), "yyyy-MM-dd HH:mm") : "";
    },
    formatCurrency(value) {
      return new Intl.NumberFormat('ar-JO', {
        style: 'currency',
        currency: 'JOD'
      }).format(value || 0);
    },
    statusClass(status) {
      return `status-${status.toLowerCase().replace(/[()]/g, '').replace(/\s+/g, '-')}`;
    },
    translateStatus(status) {
      const statusMap = {
        'DoctorReady(p)': 'تحت الإجراء',
        'lab': 'في المختبر',
        'end': 'مكتمل'
      };
      return statusMap[status] || status;
    },
    extractTeethNumbers(description) {
      if (!description) return '--';
      const match = description.match(/Teeth numbers: ([\d,\s]+)/);
      return match ? match[1] : '--';
    },
    async fetchOrders() {
      if (!this.isMounted) return;

      this.loading = true;
      this.error = null;

      if (this.cancelToken) {
        this.cancelToken.cancel("New request initiated");
      }

      this.cancelToken = axios.CancelToken.source();
      const token = localStorage.getItem("token");

      try {
        const { data } = await axios.get(`${this.baseUrl}/saved/get-orders`, {
          headers: { Authorization: `Bearer ${token}` },
          cancelToken: this.cancelToken.token
        });

        if (!data.orders || data.orders === null) {
          this.orderGroups = [];
        } else {
          this.orderGroups = Array.isArray(data.orders) ? data.orders : [data.orders];
        }
        console.log("Order groups:", this.orderGroups);

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
    async sendSavedOrders(group) {
      try {
        const token = localStorage.getItem("token");
        const orderIds = group.orders.filter(order => order.save).map(order => order._id);
        if (orderIds.length === 0) {
          alert("لا توجد طلبات محفوظة للإرسال");
          return;
        }

        // Debug log to verify payload
        console.log("Sending saved orders:", { orderIds, labId: group.labId });

        await axios.post(`${this.baseUrl}/saved/send/${group.labId}`,
            { orderIds, save: false }, // Explicitly set save to false
            { headers: { Authorization: `Bearer ${token}` } }
        );

        alert("تم إرسال الطلبات بنجاح");
        this.fetchOrders();
      } catch (error) {
        console.error("Error in sendSavedOrders:", error);
        if (error.response && error.response.data && error.response.data.message && error.response.data.message.includes("E11000 duplicate key")) {
          this.error = "خطأ: لا يمكن إرسال الطلبات لأن هناك طلبات مكررة لهذا الطبيب. يرجى التواصل مع الدعم.";
        } else {
          this.handleError(error);
        }
      }
    },
    applyFilters() {
      let filtered = [...(this.orderGroups || [])];

      if (this.activeFilter !== "all") {
        filtered = filtered.map(group => ({
          ...group,
          orders: group.orders.filter(order =>
              order.status.includes(this.activeFilter)
          )
        })).filter(group => group.orders.length > 0);
      }

      if (this.startDate && this.endDate) {
        const start = new Date(this.startDate);
        const end = new Date(this.endDate);
        end.setHours(23, 59, 59, 999);

        filtered = filtered.map(group => ({
          ...group,
          orders: group.orders.filter(order => {
            const orderDate = new Date(order.createdAt);
            return orderDate >= start && orderDate <= end;
          })
        })).filter(group => group.orders.length > 0);
      }

      if (this.searchTerm.trim()) {
        const term = this.searchTerm.toLowerCase().trim();
        filtered = filtered.map(group => ({
          ...group,
          orders: group.orders.filter(order => {
            if (this.searchField === 'all') {
              return (
                  (order.UID?.toString().includes(term)) ||
                  (order.patientName?.toLowerCase().includes(term)) ||
                  (order.status?.toLowerCase().includes(term))
              );
            }
            const fieldValue = order[this.searchField]?.toString().toLowerCase();
            return fieldValue?.includes(term);
          })
        })).filter(group => group.orders.length > 0);
      }

      this.orderGroups = filtered;
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
      let message = "حدث خطأ ما";
      if (error.response) {
        const { status, data } = error.response;
        switch (status) {
          case 400:
            message = data.message || "طلب غير صالح";
            break;
          case 401:
            message = "انتهت الجلسة، يرجى تسجيل الدخول مرة أخرى";
            this.$router.push("/login");
            return;
          case 500:
            message = "خطأ في الخادم، يرجى المحاولة لاحقاً";
            break;
        }
      } else {
        message = "خطأ في الشبكة، يرجى التحقق من اتصال الإنترنت";
      }

      this.error = message;
    },
  },
};
</script>

<style scoped>
.content {
  padding: 80px 20px 20px;
  max-width: 1200px;
  margin: 0 auto;
}

h2 {
  color: #333;
  margin-bottom: 20px;
  text-align: center;
}

.controls {
  margin-bottom: 20px;
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
  justify-content: center;
}

.search-box {
  display: flex;
  gap: 10px;
  align-items: center;
}

.search-box input {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  min-width: 250px;
}

.search-box select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
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

.date-filter button {
  padding: 8px 12px;
  background: #f8f9fa;
  border: 1px solid #ddd;
  border-radius: 4px;
  cursor: pointer;
}

.status-filters {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.status-filters button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  transition: all 0.3s;
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
  margin-top: 20px;
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
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

.card h3 {
  margin: 0 0 15px;
  color: #2c3e50;
  font-size: 1.2rem;
  border-bottom: 2px solid #00c3ff;
  padding-bottom: 8px;
}

.order-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.order-item {
  display: block;
  text-decoration: none;
  background: #f9f9f9;
  border: 1px solid #eee;
  border-radius: 6px;
  padding: 10px;
  transition: background-color 0.2s;
}

.order-item:hover {
  background: #e6f3ff;
}

.order-header {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.order-patient, .order-uid {
  font-weight: bold;
  color: #333;
  font-size: 0.9rem;
}

.order-status {
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 0.8rem;
  font-weight: bold;
  align-self: flex-start;
}

.status-doctorready-p {
  background-color: #fff3cd;
  color: #856404;
}

.status-lab {
  background-color: #d1ecf1;
  color: #0c5460;
}

.status-end {
  background-color: #d4edda;
  color: #155724;
}

.order-actions {
  margin-top: 15px;
  display: flex;
  justify-content: flex-end;
}

.send-order-btn {
  padding: 8px 16px;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.send-order-btn:hover {
  background-color: #218838;
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

.no-orders {
  padding: 20px;
  text-align: center;
  color: #666;
  font-size: 1.1rem;
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

@media (max-width: 768px) {
  .controls {
    flex-direction: column;
  }

  .search-box {
    width: 100%;
  }

  .order-cards {
    grid-template-columns: 1fr;
  }
}
</style>
```