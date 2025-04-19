<template>
  <DoctorNavbar />
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

        <!-- Lab Selection -->
        <label for="labId">Lab:</label>
        <select v-model="orderData.labId" id="labId" @change="updateTeethTypes">
          <option value="" disabled>Select a lab</option>
          <option v-for="lab in labs" :key="lab._id" :value="lab._id">
            {{ lab.username }}
          </option>
        </select>
        <span class="error" v-if="errors.labId">{{ errors.labId }}</span>

        <!-- Type of Teeth (Dynamic based on selected lab) -->
        <label for="toothType">Type of Teeth:</label>
        <select id="toothType" v-model="orderData.type" required>
          <option value="" disabled>Select tooth type</option>
          <option v-for="(price, type) in availableTeethTypes" :key="type" :value="type">
            {{ type }} ({{ formatCurrency(price) }})
          </option>
        </select>
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

        <!-- Notes -->
        <label for="notes">Notes:</label>
        <textarea
            id="notes"
            v-model="orderData.description"
            @input="formatNote"
        ></textarea>
        <span class="error" v-if="errors.description">{{ errors.description }}</span>

        <!-- Price (auto-calculated) -->
        <label>Price:</label>
        <input
            type="text"
            :value="formatCurrency(calculatedPrice)"
            readonly
        />

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
import axios from "axios";
import html2pdf from "html2pdf.js";
import DoctorNavbar from "@/components/navbars/doctorNavbar.vue";

export default {
  name: "NewOrder",
  data() {
    return {
      isActiveSidebar: false,
      labs: [],
      selectedTeeth: [],
      orderId: null,
      availableTeethTypes: {},
      orderData: {
        patientName: "",
        age: "",
        sex: "",
        teethNo: "",
        color: "",
        type: "",
        description: "",
        price: "",
        deadline: "",
        labId: "",
        images: [],
        video: null,
        file: null,
        prova: true,
      },
      baseUrl: "https://rr-5d46.onrender.com",
      errors: {},
      upperTeeth: [17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32],
      lowerTeeth: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15],
    };
  },
  components: {
    DoctorNavbar
  },
  computed: {
    calculatedPrice() {
      if (this.orderData.type && this.availableTeethTypes[this.orderData.type]) {
        // Get the base price for the selected teeth type
        const basePrice = this.availableTeethTypes[this.orderData.type];
        // Get the number of teeth (convert to number, default to 0)
        const teethCount = parseInt(this.orderData.teethNo) || 0;
        // Calculate total price: base price × number of teeth
        return basePrice * teethCount;
      }
      return 0;
    }
  },
  created() {
    this.fetchLabs();
  },
  methods: {
    async fetchLabs() {
      try {
        const token = localStorage.getItem("token");
        const response = await axios.get(`${this.baseUrl}/orders/getMyLabs`, {
          headers: { Authorization: `Bearer ${token}` },
        });
        this.labs = response.data.labs.myLabs;
        // Store the doctorId from the response if available
        if (response.data.labs.contract) {
          this.doctorId = response.data.labs.contract.doctorId;
        }
      } catch (error) {
        console.error("Error fetching labs:", error);
        this.showError("Failed to load labs. Please try again.");
      }
    },


    updateTeethTypes() {
      // Reset teeth selection when changing labs
      this.selectedTeeth = [];
      this.orderData.teethNo = "0";

      // Find the lab selected by the user
      const selectedLab = this.labs.find(lab => lab._id === this.orderData.labId);

      if (selectedLab) {
        // Get doctorId from localStorage or component data
        const doctorId = this.doctorId || JSON.parse(localStorage.getItem("doctor"))?.id;

        if (!doctorId) {
          this.showError("Doctor information not found. Please login again.");
          return;
        }

        // Find the contract for the current doctor in the selected lab
        const contract = selectedLab.contracts.find(
            c => c.doctorId?.toString() === doctorId.toString()
        );

        if (contract && contract.teethTypes) {
          this.availableTeethTypes = contract.teethTypes;
          // Auto-select the first teeth type if available
          const firstType = Object.keys(contract.teethTypes)[0];
          if (firstType) {
            this.orderData.type = firstType;
          }
        } else {
          this.availableTeethTypes = {};
          this.orderData.type = "";
          this.showError("No contract found with this lab for the current doctor.");
        }
      } else {
        this.availableTeethTypes = {};
        this.orderData.type = "";
        this.showError("Selected lab not found.");
      }
    },

    formatCurrency(value) {
      return new Intl.NumberFormat('en-JO', {
        style: 'currency',
        currency: 'JOD'
      }).format(value || 0);
    },

    formatNote() {
      this.orderData.description = this.orderData.description.replace(
          /(\d+)\s/g,
          "$1, "
      );
    },

    handleFileUpload(event, field) {
      if (field === "images") {
        this.orderData.images = Array.from(event.target.files).slice(0, 3);
      } else {
        this.orderData[field] = event.target.files[0];
      }
    },

    toggleTooth(toothNumber) {
      if (this.selectedTeeth.includes(toothNumber)) {
        this.selectedTeeth = this.selectedTeeth.filter(n => n !== toothNumber);
      } else {
        this.selectedTeeth.push(toothNumber);
      }
      // Update teeth count
      this.orderData.teethNo = this.selectedTeeth.length.toString();

      // Force price recalculation by triggering a reactive update
      // This is handled automatically by Vue's reactivity system
    },

    validateForm() {
      this.errors = {};
      let isValid = true;

      const requiredFields = [
        "patientName",
        "age",
        "sex",
        "teethNo",
        "color",
        "type",
        "deadline",
        "labId",
      ];

      requiredFields.forEach(field => {
        if (!this.orderData[field]) {
          this.errors[field] = `${this.getFieldLabel(field)} is required`;
          isValid = false;
        }
      });

      if (this.orderData.age && (this.orderData.age < 0 || this.orderData.age > 120)) {
        this.errors.age = "Please enter a valid age";
        isValid = false;
      }

      if (this.orderData.teethNo && this.orderData.teethNo < 1) {
        this.errors.teethNo = "Please select at least one tooth";
        isValid = false;
      }

      return isValid;
    },

    getFieldLabel(field) {
      const labels = {
        patientName: "Patient Name",
        age: "Age",
        sex: "Gender",
        teethNo: "Number of Teeth",
        color: "Color",
        type: "Type",
        description: "Requirements",
        deadline: "Deadline Date",
        labId: "Lab",
      };
      return labels[field] || field;
    },

    async submitOrder() {
      if (!this.validateForm()) {
        return;
      }

      try {
        const formData = new FormData();

        // Add the selected teeth numbers to the description/notes
        if (this.selectedTeeth.length > 0) {
          const teethNote = `Teeth numbers: ${this.selectedTeeth.join(', ')}. `;
          this.orderData.description = teethNote + (this.orderData.description || '');
        }

        // Set the price from the selected type
        this.orderData.price = this.calculatedPrice;

        // Append all order data to FormData
        Object.keys(this.orderData).forEach(key => {
          if (key === "images") {
            this.orderData.images.forEach((image, index) => {
              formData.append(`image${index}`, image);
            });
          } else if (this.orderData[key] !== null && this.orderData[key] !== undefined) {
            formData.append(key, this.orderData[key]);
          }
        });

        const response = await axios.post(
            `${this.baseUrl}/orders/create`,
            formData,
            {
              headers: {
                "Content-Type": "multipart/form-data",
                Authorization: `Bearer ${localStorage.getItem("token")}`,
              },
            }
        );
        console.log(response.data);

        // Generate Order ID
        const randomLetters =
            String.fromCharCode(65 + Math.floor(Math.random() * 26)) +
            String.fromCharCode(65 + Math.floor(Math.random() * 26));
        const randomDigits = Math.floor(10 + Math.random() * 90);
        this.orderId = `Order ID: ${randomLetters}${randomDigits}`;

        this.showSuccess("Order created successfully!");
        this.resetForm();
      } catch (error) {
        this.handleApiError(error);
      }
    },

    handleApiError(error) {
      if (error.response) {
        switch (error.response.status) {
          case 400:
            this.showError(
                error.response.data.message || "Invalid data. Please check your inputs."
            );
            break;
          case 401:
            this.showError("Unauthorized. Please login again.");
            break;
          case 500:
            this.showError("Server error. Please try again later.");
            break;
          default:
            this.showError("An error occurred. Please try again.");
        }
      } else {
        this.showError("Network error. Please check your connection.");
      }
    },

    showError(message) {
      alert(message);
    },

    showSuccess(message) {
      alert(message);
    },

    resetForm() {
      this.orderId = null;
      this.selectedTeeth = [];
      this.orderData = {
        patientName: "",
        age: "",
        sex: "",
        teethNo: "",
        color: "",
        type: "",
        description: "",
        price: "",
        deadline: "",
        labId: "",
        images: [],
        video: null,
        file: null,
        prova: true,
      };
      this.errors = {};
    },

    printForm() {
      const element = this.$refs.formContainer;
      const opt = {
        margin: 0.5,
        filename: "Dental_Order.pdf",
        image: { type: "jpeg", quality: 0.98 },
        html2canvas: { scale: 2 },
        jsPDF: { unit: "in", format: "letter", orientation: "portrait" },
      };
      html2pdf().set(opt).from(element).save();
    },
  },
};
</script>

<style scoped>
/* Your existing styles remain the same */
</style>
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