<template>
  <div class="application">
    <div class="application-options">
      <!-- Add buttons to switch between sections -->
      <button class="btn btn-info mb-4 text-white" @click="selectSection('GramPanchayat')">Gram Panchayat</button>
      <button class="btn btn-info mb-4 text-white" @click="selectSection('PanchayatSamiti')">Panchayat Samiti</button>
      <button class="btn btn-info mb-4 text-white" @click="selectSection('ZillaParishad')">Zilla Parishad</button>

      <!-- Display the selected table based on the section -->
      <div v-if="selectedSection === 'GramPanchayat'">
        <table class="table table-striped">
          <!-- Table headers for Gram Panchayat -->
          <thead>
            <tr>
              <th>#</th>
              <th>Date of Creation</th>
              <th>First Name</th>
              <th>Last Name</th>
              <th>Form Number</th>
              <th>Gram Panchayat Authority</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(application, index) in gramPanchayatApplications" :key="application.id">
              <!-- Display Gram Panchayat applications -->
              <td>{{ ++index }}</td>
              <td>{{ application.dtDateOfCreation }}</td>
              <td>{{ application.ncharFirstName }}</td>
              <td>{{ application.ncharLastName }}</td>
              <td>{{ application.ncharFormNumber }}</td>
              <td>
                <!-- Check if dtGramPanchayatAuthDate is present and ynGramPanchayatAuthorized is true -->
                <template v-if="application.dtGramPanchayatAuthDate && application.ynGramPanchayatAuthorized">
                  <!-- Show the result from ynGramPanchayatAuthorized -->
                  {{ application.ynGramPanchayatAuthorized }}
                </template>
                <!-- If dtGramPanchayatAuthDate is not present or ynGramPanchayatAuthorized is false, show buttons -->
                <template v-else>
                  <button class="btn btn-sm rounded btn-success me-2" @click="authorizeApplication(application.id, 'GramPanchayat')" v-if="!application.dtGramPanchayatAuthDate">
                    Authorize
                  </button>
                  <button class="btn btn-sm rounded btn-danger" @click="rejectApplication(application.id, 'GramPanchayat')" v-if="!application.dtGramPanchayatAuthDate">
                    Reject
                  </button>
                </template>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div v-if="selectedSection === 'PanchayatSamiti'">
  <table class="table table-striped">
    <!-- Table headers for Panchayat Samiti -->
    <thead>
      <tr>
        <th>#</th>
        <th>Date of Creation</th>
        <th>First Name</th>
        <th>Last Name</th>
        <th>Form Number</th>
        <th>Panchayat Samiti Authority</th>
        <th>Action</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(application, index) in panchayatSamitiApplications" :key="application.id">
        <!-- Display Panchayat Samiti applications -->
        <td>{{ ++index }}</td>
        <td>{{ application.dtDateOfCreation }}</td>
        <td>{{ application.ncharFirstName }}</td>
        <td>{{ application.ncharLastName }}</td>
        <td>{{ application.ncharFormNumber }}</td>
        <td>
          <!-- Check if dtPanchayatSamitiAuthDate is present and ynPanchayatSamitiAuthorized is true -->
          <template v-if="application.dtPanchayatSamitiAuthDate && application.ynPanchayatSamitiAuthorized">
            <!-- Show the result from ynPanchayatSamitiAuthorized -->
            {{ application.ynPanchayatSamitiAuthorized }}
          </template>
          <!-- If dtPanchayatSamitiAuthDate is not present or ynPanchayatSamitiAuthorized is false, show buttons -->
          <template v-else>
            <!-- Show buttons for Panchayat Samiti authorization -->
            <button class="btn btn-sm rounded btn-success me-2" @click="authorizeApplication(application.id, 'PanchayatSamiti')" v-if="!application.dtPanchayatSamitiAuthDate">
              Authorize
            </button>
            <button class="btn btn-sm rounded btn-danger" @click="rejectApplication(application.id, 'PanchayatSamiti')" v-if="!application.dtPanchayatSamitiAuthDate">
              Reject
            </button>
          </template>
        </td>
      </tr>
    </tbody>
  </table>
</div>

      <div v-if="selectedSection === 'ZillaParishad'">
        <table class="table table-striped">
          <!-- Table headers for Zilla Parishad -->
          <thead>
            <tr>
              <th>#</th>
              <th>Date of Creation</th>
              <th>First Name</th>
              <th>Last Name</th>
              <th>Form Number</th>
              <th>Zilla Parishad Authority</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(application, index) in zillaParishadApplications" :key="application.id">
              <!-- Display Zilla Parishad applications -->
              <td>{{ ++index }}</td>
              <td>{{ application.dtDateOfCreation }}</td>
              <td>{{ application.ncharFirstName }}</td>
              <td>{{ application.ncharLastName }}</td>
              <td>{{ application.ncharFormNumber }}</td>
              <td>
                <!-- Check if dtZillaParishadAuthDate is present and ynZillaParishadAuthorized is true -->
                <template v-if="application.dtZillaParishadAuthDate && application.ynZillaParishadAuthorized">
                  <!-- Show the result from ynZillaParishadAuthorized -->
                  {{ application.ynZillaParishadAuthorized }}
                </template>
                <!-- If dtZillaParishadAuthDate is not present or ynZillaParishadAuthorized is false, show buttons -->
                <template v-else>
                  <button class="btn btn-sm rounded btn-success me-2" @click="authorizeApplication(application.id, 'ZillaParishad')" v-if="!application.dtZillaParishadAuthDate">
                    Authorize
                  </button>
                  <button class="btn btn-sm rounded btn-danger" @click="rejectApplication(application.id, 'ZillaParishad')" v-if="!application.dtZillaParishadAuthDate">
                    Reject
                  </button>
                </template>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
//import MultiStepForm from "@/components/MultiStepForm.vue";
import axios from 'axios';

export default {
  name: 'ApplicationPage',
  components: {
   // MultiStepForm,
    // ... (other components)
  },
  data() {
    return {
      selectedSection: 'GramPanchayat', // Initialize with Gram Panchayat as the default section
      applications: [], // Initialize an empty array to store applications
    };
  },
  computed: {
    // Create computed properties for each table based on the selected section
    gramPanchayatApplications() {
      return this.applications.filter(application => !application.dtGramPanchayatAuthDate);
    },
    panchayatSamitiApplications() {
  return this.applications.filter(application => application.dtGramPanchayatAuthDate && !application.dtPanchayatSamitiAuthDate);
},
    zillaParishadApplications() {
      return this.applications.filter(application => application.dtPanchayatSamitiAuthDate);
    },
    selectedOptionComponent() {
      return this.selectedOption === 'new' ? 'MultiStepForm' : null;
    }
  },
  methods: {
    selectSection(section) {
      this.selectedSection = section;
    },
    authorizeApplication(applicationId, authority) {
    const jsonData = {
      id: applicationId,
    };

    axios
      .post(`http://127.0.0.1:5555/authorize${authority}`, jsonData, {
        headers: { 'Content-Type': 'application/json' },
      })
      .then((response) => {
        // Handle successful authorization
        // Update your frontend accordingly
        const authorizationDate = response.data.dtGramPanchayatAuthDate;
        console.log(authorizationDate)
        this.fetchApplications();
      })
      .catch((error) => {
        // Handle error
        console.error('Error authorizing application:', error);
      });
  },
  rejectApplication(applicationId, authority) {
    const jsonData = {
      id: applicationId,
    };

    axios
      .post(`http://127.0.0.1:5555/reject${authority}`, jsonData, {
        headers: { 'Content-Type': 'application/json' },
      })
      .then((response) => {
        // Handle successful rejection
        // Update your frontend accordingly
        const authorizationDate = response.data.dtGramPanchayatAuthDate;
        console.log(authorizationDate)
        this.fetchApplications();
      })
      .catch((error) => {
        // Handle error
        console.error('Error rejecting application:', error);
      });
  },
    selectOption(option) {
      this.selectedOption = option;
    },
    reloadApplicationData() {
      this.fetchApplications();
    },
    deleteApplication(applicationId) {
      const jsonData = { id: applicationId };

      axios.post('http://127.0.0.1:5555/deleteVihirInfo', jsonData, { headers: { 'Content-Type': 'application/json' } })
        .then(response => {
          // Handle successful deletion
          const deletedApplicationId = response.data.id;
          console.log(`Application with ID ${deletedApplicationId} has been deleted.`);

          // Remove the deleted application from the frontend
          this.applications = this.applications.filter(application => application.id !== applicationId);
          this.reloadApplicationData();
        })
        .catch(error => {
          // Handle error
          console.error('Error deleting application:', error);
        });
    },
    fetchApplications() {
      axios.get('http://127.0.0.1:5555/GetVihirInfo')
        .then(applications => this.applications = applications.data)
        .catch(error => console.error('Error:', error));
    },
    editApplication(applicationId) {
      this.selectedOption = 'new'; // Set the selectedOption to 'new'
      this.selectedApplicationId = applicationId; // Store the selected application ID
    },
    goBackFromChild() {
      this.selectedOption = null;
      this.reloadApplicationData();
    }
  },
  mounted() {
    this.fetchApplications(); // Fetch applications when the component is mounted
  }
};
</script>

<style scoped>
.application {
  padding: 40px;
  background-color: #ffffff;
  box-shadow: 0 0 2px var(--grey-color-light);
}

.application-options {
  /* Add your custom styles for the application options container */
}
</style>
