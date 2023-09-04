<template>
  <div>
    <h1 class="text-center">Submitted Application Forms</h1>
    
    <!-- Search Bar -->
    <input
  v-if="showInputField"
  v-model="searchQuery"
  :placeholder="selectedFilter === 'category' ? 'Enter Category' : 'Search by Name'"
  :type="selectedFilter === 'date' ? 'date' : 'text'"
  class="form-control mt-3"
/>
    
    <!-- Filter Options -->
    <div class="filter-options mt-3">
      <label>Filter By:</label>
      <select v-model="selectedFilter">
        <option value="name">Name</option>
        <option value="date">Date Range</option>
        <option value="panchayatSamiti">Panchayat Samiti Authorization</option>
        <option value="gramPanchayat">Gram Panchayat Authorization</option>
        <option value="jilhaParishad">ZP Authorization</option>
        <option value="category">Category</option>
      </select>

      <!-- Date Range Inputs (Show only if "Date Range" filter is selected) -->
      <div v-if="selectedFilter === 'date'" class="date-range">
        <label>Start Date:</label>
        <input type="date" v-model="startDate" />
        <label>End Date:</label>
        <input type="date" v-model="endDate" />
      </div>
    </div>
    
    
    <!-- Table -->
    <table class="table mt-3">
      <thead>
        <tr>
          <th>Date</th>
          <th>Name</th>
          <th>Category</th>
          <th>Gram Panchayat Authorized</th>
          <th>Panchayat Samiti Authorized</th>
          <th>Jilha Parishad Authorized</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(application, index) in filteredApplications" :key="index">
          <td>{{ application.date }}</td>
          <td>
            {{ application.firstName }} {{ application.middleName }} {{ application.lastName }}
          </td>
          <td>{{ application.category }}</td>
          <td>{{ application.gramPanchayatAuthorized }}</td>
          <td>
            {{ application.panchayatSamitiAuthorized }}
            <span v-if="application.panchayatSamitiAuthorized === 'No'" class="remark">Remark: No authorization</span>
          </td>
          <td>
            {{ application.jilhaParishadAuthorized }}
            <span v-if="application.jilhaParishadAuthorized === 'No'" class="remark">Remark: No authorization</span>
          </td>
          <td>
            <button class="btn btn-primary" @click="editApplication(application)">Edit</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: '',
      selectedFilter: 'name', // Default filter option
      startDate: '',
      endDate: '',
      applications: [
        // Dummy data (Replace with actual data)
        {
          firstName: 'John',
          middleName: 'Doe',
          lastName: 'Smith',
          category: 'abc',
          date: '2023-08-19',
          gramPanchayatAuthorized: 'Yes',
          panchayatSamitiAuthorized: 'No',
          jilhaParishadAuthorized: 'No',
          // ... other fields ...
        },
        // Add more application objects here
      ],
    };
  },
  computed: {
    showInputField() {
    return this.selectedFilter !== 'date';
  },
  filteredApplications() {
    return this.applications.filter(application => {
      const matchesSearchQuery = this.matchesSearchQuery(application);

      if (this.selectedFilter === 'name') {
        return matchesSearchQuery;
      } else if (this.selectedFilter === 'date') {
        // Apply date range filter
        return (
          matchesSearchQuery &&
          (!this.startDate || application.date >= this.startDate) &&
          (!this.endDate || application.date <= this.endDate)
        );
      } else if (this.selectedFilter === 'panchayatSamiti') {
        return matchesSearchQuery && application.panchayatSamitiAuthorized === 'Yes';
      } else if (this.selectedFilter === 'gramPanchayat') {
        return matchesSearchQuery && application.gramPanchayatAuthorized === 'Yes';
      } else if (this.selectedFilter === 'jilhaParishad') {
        return matchesSearchQuery && application.jilhaParishadAuthorized === 'Yes';
      } else if (this.selectedFilter === 'category') {
        // Filter by category
        console.log('Category:', application.category.toLowerCase());
        console.log('Search Query:', this.searchQuery.toLowerCase());
        
        return (
          matchesSearchQuery &&
          (application.category.toLowerCase() === this.searchQuery.toLowerCase())
        );
      }
    });
  },
},
  methods: {
    matchesSearchQuery(application) {
      const fullName = `${application.firstName} ${application.middleName} ${application.lastName}`;
      return fullName.toLowerCase().includes(this.searchQuery.toLowerCase());
    },
    editApplication(application) {
      console.log('Editing:', application);
      this.$emit('update:activeTab', 'edit_form');
    },
  },
};
</script>

<style scoped>
/* Table Styles */
.table {
  width: 100%;
  border-collapse: collapse;
  border-spacing: 0;
}

.table th,
.table td {
  padding: 0.75rem;
  border: 1px solid #dee2e6;
}

.table th {
  background-color: #f8f9fa;
  font-weight: bold;
  text-align: left;
}

/* Search Bar Styles */
.form-control {
  width: 100%;
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  line-height: 1.5;
  color: #495057;
  border: 1px solid #ced4da;
  border-radius: 0.25rem;
  transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

/* Button Styles */
.btn {
  display: inline-block;
  font-weight: 400;
  color: #212529;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
  user-select: none;
  border: 1px solid transparent;
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  line-height: 1.5;
  border-radius: 0.25rem;
  transition: color 0.15s ease-in-out, background-color 0.15s ease-in-out,
    border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

.btn-primary {
  color: #fff;
  background-color: #007bff;
  border-color: #007bff;
}

.btn-primary:hover {
  color: #fff;
  background-color: #0056b3;
  border-color: #0056b3;
}
/* Remark Styles */
.remark {
  color: red;
  font-size: 0.75rem;
  display: block;
  margin-top: 0.25rem;
}
</style>