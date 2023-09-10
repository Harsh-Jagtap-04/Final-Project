<template>
  <div class="application">
    <div class="application-options">
      <!-- Add buttons to switch between sections -->
      <button v-if="gramPanchayatOprator" class="btn btn-info mb-4 text-white me-1">Gram Panchayat Oprator</button>
      <button v-if="gramPanchayt" class="btn btn-info mb-4 text-white me-1">Gram Panchayat</button>
      <button v-if="panchaytSamitiOprator" class="btn btn-info mb-4 text-white me-1">Panchayat Samiti Oprator</button>
      <button v-if="panchaytSamiti" class="btn btn-info mb-4 text-white me-1">Panchayat Samiti</button>
      <button v-if="zillaParishad" class="btn btn-info mb-4 text-white me-1">Zilla Parishad</button>

      <!-- Display the selected table based on the section -->
      <div >
        <table class="table table-striped">
          <!-- Table headers for Gram Panchayat -->
          <thead>
            <tr>
              <th>#</th>
              <th>Date of Creation</th>
              <th>First Name</th>
              <th>Last Name</th>
              <th>Form Number</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(application, index) in this.applications" :key="application.id">
              <td>{{ ++index }}</td>
              <td>{{ formatDate(application.dtDateOfCreation) }}</td>
              <td>{{ application.ncharFirstName }}</td>
              <td>{{ application.ncharLastName }}</td>
              <td>{{ application.ncharFormNumber }}</td>
              <td>
                <button class="btn btn-sm btn-info rounded text-white px-3 me-1" @click="selectOption('MultiStepForm')">View</button>
                <button class="btn btn-sm btn-primary rounded text-white px-3 me-1" @click="selectOption('MultiStepForm')">Edit</button>
                <button class="btn btn-sm btn-danger rounded text-white px-3 me-1" @click="deleteApplication(application.id)">Delete</button>
                <!-- Check if dtGramPanchayatAuthDate is present and ynGramPanchayatAuthorized is true -->
                <template v-if="application.dtGramPanchayatAuthDate && application.ynGramPanchayatAuthorized">
                  <!-- Show the result from ynGramPanchayatAuthorized -->
                  <!-- {{ application.ynGramPanchayatAuthorized }} -->
                </template>
                <!-- If dtGramPanchayatAuthDate is not present or ynGramPanchayatAuthorized is false, show buttons -->
                <template v-else>
                  <!-- <button class="btn btn-sm rounded btn-success me-2" @click="authorizeApplication(application.id, 'GramPanchayat')" v-if="!application.dtGramPanchayatAuthDate">Authorize</button> -->
                  <!-- <button class="btn btn-sm rounded btn-danger" @click="rejectApplication(application.id, 'GramPanchayat')" v-if="!application.dtGramPanchayatAuthDate">Reject</button> -->
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
import Swal from 'sweetalert2';

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
      gramPanchayatOprator  : false,
      gramPanchayt          : false,
      panchaytSamitiOprator : false,
      panchaytSamiti        : false,
      zillaParishad         : false,
      user       : (localStorage.getItem('login')) ? JSON.parse(localStorage.getItem('login')) : alert('User not logged in.'),
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
  rejectApplication(applicationId, authority)
  {


    const jsonData = {id: applicationId};

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
    deleteApplication(applicationId)
    {
      Swal.fire({
        title: 'Do you want to save the changes?',
        showCancelButton: true,
        confirmButtonText: 'Delete',
      }).then((result) => {
        if (result.isConfirmed) {
          Swal.fire('Saved!' + applicationId, '', 'success')
        } else if (result.isDenied) {
          Swal.fire('Changes are not saved', '', 'info')
        }
      })

      // const jsonData = { id: applicationId };

      // axios.post('http://127.0.0.1:5555/deleteVihirInfo', jsonData, { headers: { 'Content-Type': 'application/json' } })
      // .then(response =>
      //   {
      //     const deletedApplicationId = response.data.id;
      //     console.log(`Application with ID ${deletedApplicationId} has been deleted.`);

      //     // Remove the deleted application from the frontend
      //     this.applications = this.applications.filter(application => application.id !== applicationId);
      //     this.reloadApplicationData();
      //   })
      //   .catch(error => {
          
      //     console.error('Error deleting application:', error);
      //   });
    },
    fetchApplications()
    {
      axios.get('http://127.0.0.1:5555/GetVihirInfo')
      .then(applications =>
      {
        console.log(applications)
          if(this.gramPanchayatOprator === true)
          {
            alert('Gram Panchayt Oprator Login');
          }
          else if(this.gramPanchayt === true)
          {
            this.applications = applications.data.filter(application => !application.dtGramPanchayatAuthDate);
          }
          else if(this.panchaytSamitiOprator === true)
          {
            alert('Panchayt Samiti Oprator');
          }
          else if(this.panchaytSamiti === true)
          {
            this.applications = applications.data.filter(application => application.dtGramPanchayatAuthDate && !application.dtPanchayatSamitiAuthDate);
          }
          else if(this.zillaParishad === true)
          {
            this.applications = applications.data.filter(application => application.dtPanchayatSamitiAuthDate);
          }
          else alert('Invalid Login');
      })
      .catch(error => console.error('Error:', error));
    },
    editApplication(applicationId) {
      this.selectedOption = 'new'; // Set the selectedOption to 'new'
      this.selectedApplicationId = applicationId; // Store the selected application ID
    },
    goBackFromChild() {
      this.selectedOption = null;
      this.reloadApplicationData();
    },
    formatDate(dateString)
    {
      try
      {
        var options = { year: 'numeric', month: 'short', day: 'numeric', hour: 'numeric', minute: 'numeric' };
        var date = new Date(dateString);
        var formattedDate = date.toLocaleDateString('en-US', options);      
        return `${formattedDate.toUpperCase()}`;
      }
      catch(e)
      {
        return '-';
      }
    }
  },
  mounted()
  {
    this.gramPanchayatOprator   = (this.user['gramPanchayatOprator'] !== undefined)     ? this.user.gramPanchayatOprator      : false;
    this.gramPanchayt           = (this.user['bGramPanchayatAuthority'] !== undefined)  ? this.user.bGramPanchayatAuthority   : false;
    this.panchaytSamitiOprator  = (this.user['panchaytSamitiOprator'] !== undefined)    ? this.user.panchaytSamitiOprator     : false;
    this.panchaytSamiti         = (this.user['bPanchayatSamitiAuthority'] !== undefined)? this.user.bPanchayatSamitiAuthority : false;
    this.zillaParishad          = (this.user['bZillaParishadAuthority'] !== undefined)  ? this.user.bZillaParishadAuthority   : false;
    
    this.fetchApplications(); // Fetch applications when the component is mounted

    if(this.gramPanchayatOprator == true)
    {
      alert('Work Pending');
    }
    else if(this.gramPanchayt == true)
    {
      this.applications = this.applications.filter(application => !application.dtGramPanchayatAuthDate);
    }
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
