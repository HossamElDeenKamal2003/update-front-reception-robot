<template>
  <labNavbar />
  <!-- Form Container -->
  <div class="form-container" ref="formContainer">
    <div class="order-id-box">{{ orderId || 'Order ID: --' }}</div>

    <form @submit.prevent="submitOrder">
      <fieldset>
        <legend>Patient Information</legend>

        <!-- Status -->
        <label for="status">Status:</label>
        <select v-model="orderData.prova" id="status">
          <option :value="true">Provisional</option>
          <option :value="false">Final</option>
        </select>
        <span class="error" v-if="errors.prova">{{ errors.prova }}</span>

        <!-- Patient Name -->
        <label for="patientName">Patient's Name:</label>
        <input
            type="text"
            id="patientName"
            v-model="orderData.patientName"
            required
        />
        <span class="error" v-if="errors.patientName">{{ errors.patientName }}</span>

        <!-- Gender -->
        <label for="patientGender">Gender:</label>
        <select v-model="orderData.sex" id="patientGender">
          <option value="male">Male</option>
          <option value="female">Female</option>
        </select>
        <span class="error" v-if="errors.sex">{{ errors.sex }}</span>

        <!-- Age -->
        <label for="patientAge">Age:</label>
        <input
            type="number"
            id="patientAge"
            v-model="orderData.age"
            required
        />
        <span class="error" v-if="errors.age">{{ errors.age }}</span>

        <!-- Number of Teeth -->
        <label for="teethCount">Number of Teeth Treated:</label>
        <input
            type="number"
            id="teethCount"
            v-model="orderData.teethNo"
            required
            readonly
        />
        <span class="error" v-if="errors.teethNo">{{ errors.teethNo }}</span>

        <!-- Type of Teeth -->
        <label for="toothType">Type of Teeth:</label>
        <input type="text" id="toothType" v-model="orderData.type" required />
        <span class="error" v-if="errors.type">{{ errors.type }}</span>

        <!-- Tooth Selection -->
        <h2>اضغط على السن لاختياره</h2>
        <div
            id="tooth-description"
            class="description"
            :class="{ visible: selectedTeeth.length > 0 }"
        >
          لقد اخترت السن رقم: <span id="tooth-number">{{ selectedTeeth.join(", ") }}</span>
        </div>

        <!-- Upper Teeth (17-32) -->
        <div class="teeth-container">
          <div class="tooth-wrapper">
            <img src="../../assets/img/17.jpeg" alt="Tooth 17" class="tooth-img" :class="{ selected: selectedTeeth.includes(17) }" @click="toggleTooth(17)">
            <div class="tooth-number">17</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/18.jpeg" alt="Tooth 18" class="tooth-img" :class="{ selected: selectedTeeth.includes(18) }" @click="toggleTooth(18)">
            <div class="tooth-number">18</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/19.jpeg" alt="Tooth 19" class="tooth-img" :class="{ selected: selectedTeeth.includes(19) }" @click="toggleTooth(19)">
            <div class="tooth-number">19</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/20.jpeg" alt="Tooth 20" class="tooth-img" :class="{ selected: selectedTeeth.includes(20) }" @click="toggleTooth(20)">
            <div class="tooth-number">20</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/21.jpeg" alt="Tooth 21" class="tooth-img" :class="{ selected: selectedTeeth.includes(21) }" @click="toggleTooth(21)">
            <div class="tooth-number">21</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/22.jpeg" alt="Tooth 22" class="tooth-img" :class="{ selected: selectedTeeth.includes(22) }" @click="toggleTooth(22)">
            <div class="tooth-number">22</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/23.jpeg" alt="Tooth 23" class="tooth-img" :class="{ selected: selectedTeeth.includes(23) }" @click="toggleTooth(23)">
            <div class="tooth-number">23</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/24.jpeg" alt="Tooth 24" class="tooth-img" :class="{ selected: selectedTeeth.includes(24) }" @click="toggleTooth(24)">
            <div class="tooth-number">24</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/25.jpeg" alt="Tooth 25" class="tooth-img" :class="{ selected: selectedTeeth.includes(25) }" @click="toggleTooth(25)">
            <div class="tooth-number">25</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/26.jpeg" alt="Tooth 26" class="tooth-img" :class="{ selected: selectedTeeth.includes(26) }" @click="toggleTooth(26)">
            <div class="tooth-number">26</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/27.jpeg" alt="Tooth 27" class="tooth-img" :class="{ selected: selectedTeeth.includes(27) }" @click="toggleTooth(27)">
            <div class="tooth-number">27</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/28.jpeg" alt="Tooth 28" class="tooth-img" :class="{ selected: selectedTeeth.includes(28) }" @click="toggleTooth(28)">
            <div class="tooth-number">28</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/29.jpeg" alt="Tooth 29" class="tooth-img" :class="{ selected: selectedTeeth.includes(29) }" @click="toggleTooth(29)">
            <div class="tooth-number">29</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/30.jpeg" alt="Tooth 30" class="tooth-img" :class="{ selected: selectedTeeth.includes(30) }" @click="toggleTooth(30)">
            <div class="tooth-number">30</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/31.jpeg" alt="Tooth 31" class="tooth-img" :class="{ selected: selectedTeeth.includes(31) }" @click="toggleTooth(31)">
            <div class="tooth-number">31</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/32.jpeg" alt="Tooth 32" class="tooth-img" :class="{ selected: selectedTeeth.includes(32) }" @click="toggleTooth(32)">
            <div class="tooth-number">32</div>
          </div>
        </div>

        <!-- Lower Teeth (1-15) -->
        <div class="teeth-container">
          <div class="tooth-wrapper">
            <img src="../../assets/img/1.jpeg" alt="Tooth 1" class="tooth-img" :class="{ selected: selectedTeeth.includes(1) }" @click="toggleTooth(1)">
            <div class="tooth-number">1</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/2.jpeg" alt="Tooth 2" class="tooth-img" :class="{ selected: selectedTeeth.includes(2) }" @click="toggleTooth(2)">
            <div class="tooth-number">2</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/3.jpeg" alt="Tooth 3" class="tooth-img" :class="{ selected: selectedTeeth.includes(3) }" @click="toggleTooth(3)">
            <div class="tooth-number">3</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/4.jpeg" alt="Tooth 4" class="tooth-img" :class="{ selected: selectedTeeth.includes(4) }" @click="toggleTooth(4)">
            <div class="tooth-number">4</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/5.jpeg" alt="Tooth 5" class="tooth-img" :class="{ selected: selectedTeeth.includes(5) }" @click="toggleTooth(5)">
            <div class="tooth-number">5</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/6.jpeg" alt="Tooth 6" class="tooth-img" :class="{ selected: selectedTeeth.includes(6) }" @click="toggleTooth(6)">
            <div class="tooth-number">6</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/7.jpeg" alt="Tooth 7" class="tooth-img" :class="{ selected: selectedTeeth.includes(7) }" @click="toggleTooth(7)">
            <div class="tooth-number">7</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/8.jpeg" alt="Tooth 8" class="tooth-img" :class="{ selected: selectedTeeth.includes(8) }" @click="toggleTooth(8)">
            <div class="tooth-number">8</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/9.jpeg" alt="Tooth 9" class="tooth-img" :class="{ selected: selectedTeeth.includes(9) }" @click="toggleTooth(9)">
            <div class="tooth-number">9</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/10.jpeg" alt="Tooth 10" class="tooth-img" :class="{ selected: selectedTeeth.includes(10) }" @click="toggleTooth(10)">
            <div class="tooth-number">10</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/11.jpeg" alt="Tooth 11" class="tooth-img" :class="{ selected: selectedTeeth.includes(11) }" @click="toggleTooth(11)">
            <div class="tooth-number">11</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/12.jpeg" alt="Tooth 12" class="tooth-img" :class="{ selected: selectedTeeth.includes(12) }" @click="toggleTooth(12)">
            <div class="tooth-number">12</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/13.jpeg" alt="Tooth 13" class="tooth-img" :class="{ selected: selectedTeeth.includes(13) }" @click="toggleTooth(13)">
            <div class="tooth-number">13</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/14.jpeg" alt="Tooth 14" class="tooth-img" :class="{ selected: selectedTeeth.includes(14) }" @click="toggleTooth(14)">
            <div class="tooth-number">14</div>
          </div>
          <div class="tooth-wrapper">
            <img src="../../assets/img/15.jpeg" alt="Tooth 15" class="tooth-img" :class="{ selected: selectedTeeth.includes(15) }" @click="toggleTooth(15)">
            <div class="tooth-number">15</div>
          </div>
        </div>

        <!-- Color -->
        <label for="color">Color:</label>
        <input type="text" id="color" v-model="orderData.color" required />
        <span class="error" v-if="errors.color">{{ errors.color }}</span>

        <label for="notes">Notes:</label>
        <textarea
            id="notes"
            v-model="orderData.description"
            @input="formatNote"
        ></textarea>
        <span class="error" v-if="errors.description">{{ errors.description }}</span>

        <!-- Deadline -->
        <label for="deadline">Deadline Date:</label>
        <input
            type="date"
            id="deadline"
            v-model="orderData.deadline"
            required
        />
        <span class="error" v-if="errors.deadline">{{ errors.deadline }}</span>

        <!-- Audio -->
        <label for="audio">Audio Recording:</label>
        <input
            type="file"
            id="audio"
            accept="audio/*"
            class="file-input"
            @change="handleFileUpload($event, 'video')"
        />
        <span class="error" v-if="errors.video">{{ errors.video }}</span>

        <!-- Images -->
        <label for="photo">Images (up to 3):</label>
        <input
            type="file"
            id="photo"
            accept="image/*"
            class="file-input"
            multiple
            @change="handleFileUpload($event, 'images')"
        />
        <span class="error" v-if="errors.images">{{ errors.images }}</span>

        <!-- Submit Button -->
        <button type="submit">Submit</button>
      </fieldset>

      <!-- Print Icon -->
      <div class="print-icon" @click="printForm" title="Print as PDF">
        <font-awesome-icon icon="print" />
        <font-awesome-icon icon="file-pdf" />
      </div>
    </form>
  </div>

  <!-- Chat Icon -->
  <div class="chat-icon" title="Chat with us">
    <font-awesome-icon icon="comments" />
  </div>
</template>

<script>
import labNavbar from "@/components/navbars/labsNavbar.vue";
import axios from "axios";
import { format } from "date-fns";

export default {
  name: "ShowOrder",
  components: {
    labNavbar,
  },
  props: {
    id: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      order: null,
      orderData: {
        prova: true,
        patientName: "",
        sex: "male",
        age: null,
        teethNo: 0,
        type: "",
        color: "",
        labId: "",
        description: "",
        deadline: "",
        video: null,
        images: [],
      },
      selectedTeeth: [],
      labs: [],
      errors: {},
      orderId: null,
      loading: false,
      error: null,
      cancelToken: null,
      retryCount: 0,
      maxRetries: 3,
      upperTeeth: Array.from({ length: 16 }, (_, i) => 17 + i), // 17-32
      lowerTeeth: Array.from({ length: 15 }, (_, i) => 1 + i), // 1-15
    };
  },
  computed: {
    isAuthenticated() {
      return !!localStorage.getItem("token");
    },
  },
  created() {
    this.fetchOrder();
  },
  beforeUnmount() {
    this.cancelRequest();
  },
  methods: {
    cancelRequest() {
      if (this.cancelToken) {
        this.cancelToken.cancel("Component unmounted");
      }
    },
    formatDate(date) {
      return date ? format(new Date(date), "MMM dd, yyyy HH:mm") : "N/A";
    },
    statusClass(status) {
      if (!status) return "";
      return `status-${status.toLowerCase().replace(/[()]/g, "").replace(/\s+/g, "-")}`;
    },
    async fetchOrder() {
      this.cancelRequest();
      this.loading = true;
      this.error = null;
      this.cancelToken = axios.CancelToken.source();

      try {
        const token = localStorage.getItem("token");
        const response = await axios.get(`https://rr-5d46.onrender.com/labdash/order/${this.id}`, {
          headers: { Authorization: `Bearer ${token}` },
          cancelToken: this.cancelToken.token,
        });
        if (!response.data?.order) {
          throw new Error("Order data is empty");
        }
        this.order = response.data.order;
        this.orderId = this.order.UID;

        // Map API data to orderData
        this.orderData = {
          prova: this.order.prova || true,
          patientName: this.order.patientName || "",
          sex: this.order.sex || "male",
          age: this.order.age || null,
          teethNo: this.order.teethNo || 0,
          type: this.order.type || "",
          color: this.order.color || "",
          labId: this.order.labId || "",
          description: this.order.description || "",
          deadline: this.order.deadline ? this.order.deadline.split("T")[0] : "",
          video: null, // File inputs are not pre-filled
          images: [], // File inputs are not pre-filled
        };

        // Set selected teeth
        this.selectedTeeth = this.order.teeth || [];
        this.orderData.teethNo = this.selectedTeeth.length;

        this.retryCount = 0;
      } catch (error) {
        if (axios.isCancel(error)) {
          console.log("Request canceled:", error.message);
          return;
        }

        if (this.retryCount < this.maxRetries) {
          this.retryCount++;
          await new Promise((resolve) => setTimeout(resolve, 1000 * this.retryCount));
          return this.fetchOrder();
        }

        this.error = error.response?.data?.message || error.message || "Failed to load order details";

        if (error.response?.status === 404) {
          this.$router.push("/not-found");
        }
      } finally {
        this.loading = false;
      }
    },
    toggleTooth(tooth) {
      if (this.selectedTeeth.includes(tooth)) {
        this.selectedTeeth = this.selectedTeeth.filter((t) => t !== tooth);
      } else {
        this.selectedTeeth.push(tooth);
      }
      this.orderData.teethNo = this.selectedTeeth.length;
    },
    formatNote() {
      // Optional: Format the description (e.g., trim, capitalize)
      this.orderData.description = this.orderData.description.trim();
    },
    handleFileUpload(event, type) {
      const files = event.target.files;
      if (type === "video") {
        this.orderData.video = files[0] || null;
        if (files.length > 1) {
          this.errors.video = "Only one audio file is allowed";
        } else {
          delete this.errors.video;
        }
      } else if (type === "images") {
        this.orderData.images = Array.from(files).slice(0, 3); // Limit to 3 images
        if (files.length > 3) {
          this.errors.images = "Maximum 3 images allowed";
        } else {
          delete this.errors.images;
        }
      }
    },
    async submitOrder() {
      this.errors = {};

      // Basic validation
      if (!this.orderData.patientName) this.errors.patientName = "Patient name is required";
      if (!this.orderData.age) this.errors.age = "Age is required";
      if (!this.orderData.teethNo) this.errors.teethNo = "At least one tooth must be selected";
      if (!this.orderData.type) this.errors.type = "Tooth type is required";
      if (!this.orderData.color) this.errors.color = "Color is required";
      if (!this.orderData.labId) this.errors.labId = "Lab selection is required";
      if (!this.orderData.deadline) this.errors.deadline = "Deadline is required";

      if (Object.keys(this.errors).length > 0) {
        return;
      }

      // Prepare form data
      const formData = new FormData();
      formData.append("prova", this.orderData.prova);
      formData.append("patientName", this.orderData.patientName);
      formData.append("sex", this.orderData.sex);
      formData.append("age", this.orderData.age);
      formData.append("teethNo", this.orderData.teethNo);
      formData.append("teeth", JSON.stringify(this.selectedTeeth));
      formData.append("type", this.orderData.type);
      formData.append("color", this.orderData.color);
      formData.append("labId", this.orderData.labId);
      formData.append("description", this.orderData.description);
      formData.append("deadline", this.orderData.deadline);

      if (this.orderData.video) {
        formData.append("video", this.orderData.video);
      }
      this.orderData.images.forEach((image) => {
        formData.append(`images`, image);
      });

      try {
        this.loading = true;
        const token = localStorage.getItem("token");
        await axios.put(`https://rr-5d46.onrender.com/docdash/order/${this.id}`, formData, {
          headers: {
            Authorization: `Bearer ${token}`,
            "Content-Type": "multipart/form-data",
          },
        });
        alert("Order updated successfully!");
        this.$router.push("/orders");
      } catch (error) {
        this.error = error.response?.data?.message || "Failed to update order";
      } finally {
        this.loading = false;
      }
    },
    printForm() {
      window.print(); // Basic print functionality
      // For PDF generation, you may need a library like jsPDF
    },
  },
};
</script>

<style scoped>
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  margin: 0;
  padding: 0;
  width: 100%;
  overflow-x: hidden;
  margin: 0 auto;
  padding-top: 80px;
}


/* Form Container */
.form-container {
  position: relative;
  background-color: white;
  padding: 30px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  width: 90%; /* Use percentage for responsiveness */
  max-width: 1200px; /* Increased max-width for larger screens */
  margin: 100px auto 20px;
  box-sizing: border-box;
}

fieldset {
  border: none;
  margin-bottom: 15px;
}

label,
legend {
  font-weight: bold;
  font-size: 1rem;
}

input,
select,
textarea {
  width: 100%;
  padding: 10px;
  margin: 8px 0;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

textarea {
  height: 100px;
  resize: vertical;
}

button {
  background-color: #4caf50;
  color: white;
  padding: 12px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  width: 100%;
}

button:hover {
  background-color: #45a049;
}

.navbar-logo img {
  height: 40px;
  width: auto;
  border-radius: 4px;
}

.file-input {
  padding: 0;
}

.order-id-box {
  position: absolute;
  top: 20px;
  right: 20px;
  background-color: #4caf50;
  color: white;
  padding: 10px 15px;
  border-radius: 4px;
  font-size: 1rem;
  font-weight: bold;
  text-align: center;
}

.print-icon {
  position: absolute;
  bottom: 20px;
  left: 20px;
  font-size: 1.375rem;
  background: #4caf50;
  color: white;
  padding: 8px 12px;
  border-radius: 4px;
  cursor: pointer;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
}

.chat-icon {
  position: fixed;
  bottom: 20px;
  right: 20px;
  font-size: 1.625rem;
  background: #007bff;
  color: white;
  padding: 12px;
  border-radius: 50%;
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  z-index: 999;
}

/* Teeth Container */
.teeth-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(60px, 1fr)); /* Responsive grid */
  gap: 10px;
  justify-items: center;
  margin: 15px 0;
}

.tooth-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.tooth-img {
  width: 40px; /* Slightly larger for better visibility */
  height: auto;
  border: 2px solid transparent;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.tooth-img:hover {
  opacity: 0.7;
}

.tooth-img.selected {
  border: 2px solid rgb(0, 255, 4);
  box-shadow: 0 0 10px rgba(12, 204, 48, 0.5);
  opacity: 1;
}

.tooth-number {
  font-size: 0.75rem;
  font-weight: bold;
  margin-top: 3px;
  text-align: center;
}

.description {
  text-align: center;
  margin-top: 20px;
  font-size: 1.125rem;
  color: #333;
  visibility: hidden;
}

.description.visible {
  visibility: visible;
}

.error {
  color: red;
  font-size: 0.875rem;
  display: block;
  margin-top: 5px;
}

/* Responsive Adjustments */
@media (max-width: 768px) {
  .navbar-container {
    flex-direction: column;
    align-items: flex-start;
    padding: 10px;
  }

  .nav-links {
    flex-direction: column;
    gap: 10px;
    margin-top: 10px;
  }

  .form-container {
    width: 95%;
    padding: 20px;
    margin-top: 80px; /* Adjusted for smaller navbar */
  }

  .order-id-box {
    top: 10px;
    right: 10px;
    font-size: 0.875rem;
    padding: 8px 12px;
  }

  .print-icon {
    bottom: 10px;
    left: 10px;
    font-size: 1.125rem;
    padding: 6px 10px;
  }

  .teeth-container {
    grid-template-columns: repeat(auto-fit, minmax(50px, 1fr));
  }

  .tooth-img {
    width: 30px;
  }

  .tooth-number {
    font-size: 0.625rem;
  }
}

@media (max-width: 480px) {
  .navbar-logo {
    font-size: 1.25rem;
  }

  .form-container {
    padding: 15px;
  }

  label,
  legend {
    font-size: 0.875rem;
  }

  input,
  select,
  textarea {
    padding: 8px;
  }

  button {
    padding: 10px;
  }

  .teeth-container {
    grid-template-columns: repeat(auto-fit, minmax(40px, 1fr));
  }

  .tooth-img {
    width: 25px;
  }
}
</style>