<template>
  <div id="app">
    <div class="header">
      <div class="header-content">
        <div class="logo">
          <svg width="40" height="40" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M20 7H4C2.9 7 2 7.9 2 9V19C2 20.1 2.9 21 4 21H20C21.1 21 22 20.1 22 19V9C22 7.9 21.1 7 20 7Z" stroke="white" stroke-width="2" fill="none"/>
            <path d="M16 21V5C16 3.9 15.1 3 14 3H10C8.9 3 8 3.9 8 5V21" stroke="white" stroke-width="2" fill="none"/>
            <circle cx="12" cy="13" r="2" fill="white"/>
          </svg>
        </div>
        <div class="title-section">
          <h1>Job Application Dashboard</h1>
          <p>Manage and track all job applications in one place</p>
        </div>
      </div>
    </div>
    
    <!-- Tab Navigation -->
    <div class="tabs-container">
      <div class="tabs">
        <button 
          @click="currentTab = 'form'" 
          :class="{ active: currentTab === 'form' }"
          class="tab-btn"
        >
          <span class="tab-icon">📝</span>
          <span>Applicant Form</span>
        </button>
        <button 
          @click="currentTab = 'admin'" 
          :class="{ active: currentTab === 'admin' }"
          class="tab-btn"
        >
          <span class="tab-icon">👑</span>
          <span>Administrator Dashboard</span>
        </button>
      </div>
    </div>

    <!-- Applicant Form Tab -->
    <div v-if="currentTab === 'form'" class="form-tab">
      <div class="container">
        <div class="form-card">
          <div class="card-header">
            <h2>Applicant Information</h2>
            <p>Please fill in all the details below</p>
          </div>
          
          <!-- Success Message -->
          <div v-if="showSuccess" class="success-alert">
            <div class="alert-icon">✅</div>
            <div class="alert-content">
              <strong>Success!</strong> Your application has been submitted successfully.
            </div>
            <button class="alert-close" @click="showSuccess = false">×</button>
          </div>

          <!-- Application Form -->
          <form @submit.prevent="submitApplication" class="application-form">
            <div class="form-row">
              <div class="form-group">
                <label class="form-label">
                  <span class="label-icon">👤</span>
                  First Name
                  <span class="required">*</span>
                </label>
                <input 
                  type="text" 
                  v-model="formData.firstname" 
                  class="form-input"
                  placeholder="Enter your first name"
                  required
                >
              </div>

              <div class="form-group">
                <label class="form-label">
                  <span class="label-icon">👥</span>
                  Last Name
                  <span class="required">*</span>
                </label>
                <input 
                  type="text" 
                  v-model="formData.surname" 
                  class="form-input"
                  placeholder="Enter your last name"
                  required
                >
              </div>
            </div>

            <div class="form-row">
              <div class="form-group">
                <label class="form-label">
                  <span class="label-icon">🎂</span>
                  Date of Birth
                  <span class="required">*</span>
                </label>
                <input 
                  type="date" 
                  v-model="formData.dob" 
                  class="form-input"
                  required
                >
              </div>

              <div class="form-group">
                <label class="form-label">
                  <span class="label-icon">📱</span>
                  Phone Number
                  <span class="required">*</span>
                </label>
                <input 
                  type="tel" 
                  v-model="formData.phone" 
                  class="form-input"
                  placeholder="+1 234 567 8900"
                  required
                >
              </div>
            </div>

            <div class="form-group full-width">
              <label class="form-label">
                <span class="label-icon">🏠</span>
                Address
                <span class="required">*</span>
              </label>
              <textarea 
                v-model="formData.address" 
                rows="3" 
                class="form-textarea"
                placeholder="Enter your full address"
                required
              ></textarea>
            </div>

            <div class="form-group full-width">
              <label class="form-label">
                <span class="label-icon">⚥</span>
                Gender
                <span class="required">*</span>
              </label>
              <div class="radio-group">
                <label class="radio-label">
                  <input type="radio" value="Male" v-model="formData.gender">
                  <span class="radio-custom"></span>
                  Male
                </label>
                <label class="radio-label">
                  <input type="radio" value="Female" v-model="formData.gender">
                  <span class="radio-custom"></span>
                  Female
                </label>
                <label class="radio-label">
                  <input type="radio" value="Other" v-model="formData.gender">
                  <span class="radio-custom"></span>
                  Other
                </label>
              </div>
            </div>

            <div class="form-group full-width">
              <label class="form-label">
                <span class="label-icon">🆔</span>
                National ID
                <span class="required">*</span>
              </label>
              <input 
                type="text" 
                v-model="formData.nationalId" 
                class="form-input"
                placeholder="Enter your National ID number"
                required
              >
            </div>

            <button type="submit" class="submit-btn">
              <span class="btn-icon">📤</span>
              Submit Application
              <span class="btn-arrow">→</span>
            </button>
          </form>
        </div>
      </div>
    </div>

    <!-- Administrator Dashboard Tab -->
    <div v-if="currentTab === 'admin'" class="admin-tab">
      <div class="container">
        <div class="dashboard-card">
          <div class="card-header">
            <div class="header-stats">
              <h2>Applications Dashboard</h2>
              <div class="stats-badge">
                <span class="stats-number">{{ applications.length }}</span>
                <span class="stats-label">Total Applications</span>
              </div>
            </div>
            <p>View and manage all submitted applications</p>
          </div>
          
          <!-- No applications message -->
          <div v-if="applications.length === 0" class="empty-state">
            <div class="empty-icon">📭</div>
            <h3>No Applications Yet</h3>
            <p>Switch to the Applicant Form tab to add your first application</p>
            <button @click="currentTab = 'form'" class="empty-btn">
              + Add Your First Application
            </button>
          </div>

          <!-- Applications Table -->
          <div v-else class="table-container">
            <div class="table-controls">
              <div class="search-box">
                <span class="search-icon">🔍</span>
                <input 
                  type="text" 
                  v-model="searchQuery" 
                  placeholder="Search applications..."
                  class="search-input"
                >
              </div>
              <div class="table-info">
                Showing {{ filteredApplications.length }} of {{ applications.length }} applications
              </div>
            </div>

            <div class="table-wrapper">
              <table class="applications-table">
                <thead>
                  <tr>
                    <th>#</th>
                    <th>Full Name</th>
                    <th>Date of Birth</th>
                    <th>Address</th>
                    <th>Phone</th>
                    <th>Gender</th>
                    <th>National ID</th>
                    <th>Submitted</th>
                    <th>Actions</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(app, index) in filteredApplications" :key="index">
                    <td data-label="#">{{ index + 1 }}</td>
                    <td data-label="Full Name">
                      <strong>{{ app.firstname }} {{ app.surname }}</strong>
                    </td>
                    <td data-label="Date of Birth">{{ formatDate(app.dob) }}</td>
                    <td data-label="Address">{{ app.address }}</td>
                    <td data-label="Phone">{{ app.phone }}</td>
                    <td data-label="Gender">
                      <span :class="['gender-badge', getGenderClass(app.gender)]">
                        {{ app.gender }}
                      </span>
                    </td>
                    <td data-label="National ID">{{ app.nationalId }}</td>
                    <td data-label="Submitted">{{ app.submittedAt }}</td>
                    <td data-label="Actions">
                      <button @click="viewApplication(app)" class="action-btn view-btn" title="View Details">
                        👁️
                      </button>
                      <button @click="deleteApplication(index)" class="action-btn delete-btn" title="Delete">
                        🗑️
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- View Application Modal -->
    <div v-if="showModal" class="modal" @click.self="closeModal">
      <div class="modal-content">
        <div class="modal-header">
          <h3>Application Details</h3>
          <button class="modal-close" @click="closeModal">×</button>
        </div>
        <div class="modal-body">
          <div class="detail-item">
            <strong>Full Name:</strong> {{ selectedApplication.firstname }} {{ selectedApplication.surname }}
          </div>
          <div class="detail-item">
            <strong>Date of Birth:</strong> {{ formatDate(selectedApplication.dob) }}
          </div>
          <div class="detail-item">
            <strong>Address:</strong> {{ selectedApplication.address }}
          </div>
          <div class="detail-item">
            <strong>Phone:</strong> {{ selectedApplication.phone }}
          </div>
          <div class="detail-item">
            <strong>Gender:</strong> {{ selectedApplication.gender }}
          </div>
          <div class="detail-item">
            <strong>National ID:</strong> {{ selectedApplication.nationalId }}
          </div>
          <div class="detail-item">
            <strong>Submitted:</strong> {{ selectedApplication.submittedAt }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      currentTab: 'form',
      showSuccess: false,
      showModal: false,
      selectedApplication: {},
      searchQuery: '',
      formData: {
        firstname: '',
        surname: '',
        dob: '',
        address: '',
        phone: '',
        gender: '',
        nationalId: ''
      },
      applications: []
    }
  },
  computed: {
    filteredApplications() {
      if (!this.searchQuery) return this.applications;
      const query = this.searchQuery.toLowerCase();
      return this.applications.filter(app => 
        app.firstname.toLowerCase().includes(query) ||
        app.surname.toLowerCase().includes(query) ||
        app.phone.includes(query) ||
        app.nationalId.includes(query)
      );
    }
  },
  methods: {
    submitApplication() {
      if (!this.formData.firstname || !this.formData.surname || !this.formData.dob ||
          !this.formData.address || !this.formData.phone || !this.formData.gender ||
          !this.formData.nationalId) {
        alert('⚠️ Please fill in all fields before submitting!');
        return;
      }
      
      const newApplication = {
        ...this.formData,
        submittedAt: new Date().toLocaleString()
      };
      
      this.applications.push(newApplication);
      this.showSuccess = true;
      this.clearForm();
      
      setTimeout(() => {
        this.showSuccess = false;
      }, 3000);
    },
    
    clearForm() {
      this.formData = {
        firstname: '',
        surname: '',
        dob: '',
        address: '',
        phone: '',
        gender: '',
        nationalId: ''
      };
    },
    
    formatDate(dateString) {
      if (!dateString) return 'N/A';
      const date = new Date(dateString);
      return date.toLocaleDateString('en-US', { year: 'numeric', month: 'short', day: 'numeric' });
    },
    
    getGenderClass(gender) {
      if (gender === 'Male') return 'gender-male';
      if (gender === 'Female') return 'gender-female';
      return 'gender-other';
    },
    
    viewApplication(app) {
      this.selectedApplication = app;
      this.showModal = true;
    },
    
    closeModal() {
      this.showModal = false;
    },
    
    deleteApplication(index) {
      if (confirm('Are you sure you want to delete this application?')) {
        this.applications.splice(index, 1);
      }
    }
  }
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
}

#app {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

/* Header Styles */
.header {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
}

.header-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: 30px;
  display: flex;
  align-items: center;
  gap: 20px;
}

.logo {
  background: rgba(255, 255, 255, 0.2);
  padding: 12px;
  border-radius: 15px;
  backdrop-filter: blur(5px);
}

.title-section h1 {
  color: white;
  font-size: 32px;
  margin-bottom: 8px;
  font-weight: 700;
}

.title-section p {
  color: rgba(255, 255, 255, 0.9);
  font-size: 14px;
}

/* Tabs Container */
.tabs-container {
  background: white;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.tabs {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  gap: 10px;
  padding: 0 30px;
}

.tab-btn {
  padding: 15px 25px;
  font-size: 16px;
  font-weight: 600;
  background: none;
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
  color: #666;
  display: flex;
  align-items: center;
  gap: 8px;
  position: relative;
}

.tab-btn:hover {
  color: #667eea;
  background: #f5f5f5;
}

.tab-btn.active {
  color: #667eea;
}

.tab-btn.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 3px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.tab-icon {
  font-size: 20px;
}

/* Container */
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 40px 30px;
}

/* Form Card */
.form-card, .dashboard-card {
  background: white;
  border-radius: 20px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.card-header {
  background: linear-gradient(135deg, #f5f7fa 0%, #e8eef5 100%);
  padding: 30px;
  border-bottom: 1px solid #e0e0e0;
}

.card-header h2 {
  color: #333;
  margin-bottom: 8px;
  font-size: 24px;
}

.card-header p {
  color: #666;
  font-size: 14px;
}

.header-stats {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  flex-wrap: wrap;
  gap: 15px;
}

.stats-badge {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 10px 20px;
  border-radius: 12px;
  text-align: center;
  color: white;
}

.stats-number {
  font-size: 24px;
  font-weight: bold;
  display: block;
}

.stats-label {
  font-size: 12px;
  opacity: 0.9;
}

/* Form Styles */
.application-form {
  padding: 30px;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 20px;
}

.form-group.full-width {
  grid-column: span 2;
}

.form-label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
  color: #555;
  font-size: 14px;
}

.label-icon {
  margin-right: 5px;
}

.required {
  color: #e74c3c;
  margin-left: 3px;
}

.form-input, .form-textarea {
  width: 100%;
  padding: 12px 15px;
  border: 2px solid #e0e0e0;
  border-radius: 10px;
  font-size: 14px;
  transition: all 0.3s ease;
}

.form-input:focus, .form-textarea:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.form-textarea {
  resize: vertical;
  font-family: inherit;
}

/* Radio Group */
.radio-group {
  display: flex;
  gap: 20px;
}

.radio-label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-size: 14px;
  color: #555;
}

.radio-label input {
  display: none;
}

.radio-custom {
  width: 18px;
  height: 18px;
  border: 2px solid #ddd;
  border-radius: 50%;
  position: relative;
}

.radio-label input:checked + .radio-custom {
  border-color: #667eea;
}

.radio-label input:checked + .radio-custom::after {
  content: '';
  position: absolute;
  top: 3px;
  left: 3px;
  width: 8px;
  height: 8px;
  background: #667eea;
  border-radius: 50%;
}

/* Submit Button */
.submit-btn {
  width: 100%;
  padding: 14px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 10px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.submit-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(102, 126, 234, 0.3);
}

.btn-icon, .btn-arrow {
  font-size: 18px;
}

/* Success Alert */
.success-alert {
  margin: 20px 30px 0 30px;
  background: linear-gradient(135deg, #84fab0 0%, #8fd3f4 100%);
  padding: 15px 20px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  gap: 15px;
  animation: slideDown 0.5s ease;
}

.alert-icon {
  font-size: 24px;
}

.alert-content {
  flex: 1;
  color: #155724;
}

.alert-close {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #155724;
}

@keyframes slideDown {
  from {
    transform: translateY(-20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

/* Empty State */
.empty-state {
  text-align: center;
  padding: 80px 30px;
}

.empty-icon {
  font-size: 80px;
  margin-bottom: 20px;
}

.empty-state h3 {
  color: #333;
  margin-bottom: 10px;
  font-size: 24px;
}

.empty-state p {
  color: #666;
  margin-bottom: 30px;
}

.empty-btn {
  padding: 12px 30px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
}

/* Table Styles */
.table-container {
  padding: 30px;
}

.table-controls {
  margin-bottom: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 15px;
}

.search-box {
  position: relative;
  flex: 1;
  max-width: 300px;
}

.search-icon {
  position: absolute;
  left: 12px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 14px;
}

.search-input {
  width: 100%;
  padding: 10px 12px 10px 35px;
  border: 2px solid #e0e0e0;
  border-radius: 10px;
  font-size: 14px;
  transition: all 0.3s;
}

.search-input:focus {
  outline: none;
  border-color: #667eea;
}

.table-info {
  color: #666;
  font-size: 14px;
}

.table-wrapper {
  overflow-x: auto;
}

.applications-table {
  width: 100%;
  border-collapse: collapse;
}

.applications-table thead {
  background: linear-gradient(135deg, #f5f7fa 0%, #e8eef5 100%);
}

.applications-table th {
  padding: 15px;
  text-align: left;
  font-weight: 600;
  color: #555;
  font-size: 13px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.applications-table td {
  padding: 15px;
  border-bottom: 1px solid #e0e0e0;
  font-size: 14px;
}

.applications-table tbody tr:hover {
  background: #f9f9f9;
}

/* Gender Badges */
.gender-badge {
  display: inline-block;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
}

.gender-male {
  background: #d4f1f9;
  color: #0c5460;
}

.gender-female {
  background: #f8d7da;
  color: #721c24;
}

.gender-other {
  background: #e2d9f3;
  color: #4a2e8a;
}

/* Action Buttons */
.action-btn {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 18px;
  padding: 5px 8px;
  transition: transform 0.2s;
}

.action-btn:hover {
  transform: scale(1.1);
}

.view-btn:hover {
  color: #667eea;
}

.delete-btn:hover {
  color: #e74c3c;
}

/* Modal */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  animation: fadeIn 0.3s;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.modal-content {
  background: white;
  border-radius: 20px;
  max-width: 500px;
  width: 90%;
  max-height: 80vh;
  overflow: auto;
  animation: slideUp 0.3s;
}

@keyframes slideUp {
  from {
    transform: translateY(50px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

.modal-header {
  padding: 20px;
  border-bottom: 1px solid #e0e0e0;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.modal-header h3 {
  color: #333;
}

.modal-close {
  background: none;
  border: none;
  font-size: 28px;
  cursor: pointer;
  color: #999;
}

.modal-body {
  padding: 20px;
}

.detail-item {
  padding: 10px 0;
  border-bottom: 1px solid #f0f0f0;
}

.detail-item strong {
  display: inline-block;
  min-width: 120px;
  color: #555;
}

/* Responsive */
@media (max-width: 768px) {
  .header-content {
    flex-direction: column;
    text-align: center;
    padding: 20px;
  }
  
  .title-section h1 {
    font-size: 24px;
  }
  
  .tabs {
    padding: 0 15px;
  }
  
  .tab-btn {
    padding: 12px 15px;
    font-size: 14px;
  }
  
  .container {
    padding: 20px 15px;
  }
  
  .form-row {
    grid-template-columns: 1fr;
    gap: 0;
  }
  
  .form-group.full-width {
    grid-column: span 1;
  }
  
  .card-header, .application-form, .table-container {
    padding: 20px;
  }
  
  .header-stats {
    flex-direction: column;
    align-items: flex-start;
  }
  
  .table-controls {
    flex-direction: column;
  }
  
  .search-box {
    max-width: 100%;
  }
  
  .applications-table thead {
    display: none;
  }
  
  .applications-table tbody tr {
    display: block;
    margin-bottom: 20px;
    border: 1px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
  }
  
  .applications-table td {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px;
    border: none;
  }
  
  .applications-table td::before {
    content: attr(data-label);
    font-weight: 600;
    margin-right: 10px;
  }
}
</style>