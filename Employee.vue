<template>
  <div class="container py-5">
    <!-- Heading -->
    <div class="row justify-content-center mb-4">
      <div class="col-lg-8 text-center">
        <h2>Employee Management</h2>
        <p class="sub-title">Manage employee records easily</p>
      </div>
    </div>

    <!-- Form -->
    <div class="row justify-content-center">
      <div class="col-lg-10">
        <div class="form-card">
          <p v-if="message" class="alert alert-success text-center">
            {{ message }}
          </p>

          <form @submit.prevent="saveEmployee">
            <div class="row g-3">
              <div class="col-md-3 col-sm-6">
                <input
                  v-model="emp.name"
                  type="text"
                  placeholder="Name"
                  class="form-control"
                  required
                />
              </div>

              <div class="col-md-3 col-sm-6">
                <input
                  v-model="emp.designation"
                  type="text"
                  placeholder="Designation"
                  class="form-control"
                />
              </div>

              <div class="col-md-3 col-sm-6">
                <input
                  v-model="emp.department"
                  type="text"
                  placeholder="Department"
                  class="form-control"
                />
              </div>

              <div class="col-md-2 col-sm-6">
                <input
                  v-model="emp.salary"
                  type="text"
                  placeholder="Salary"
                  class="form-control"
                />
              </div>

              <div class="col-md-1 col-sm-12 d-grid">
                <button type="submit" class="btn btn-primary">
                  {{ editMode ? "Update" : "Add" }}
                </button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- Table -->
    <div class="row mt-4">
      <div class="col-12">
        <div class="table-card">
          <div class="table-responsive">
            <table class="table table-hover align-middle text-center mb-0">
              <thead>
                <tr>
                  <th>Name</th>
                  <th>Designation</th>
                  <th>Department</th>
                  <th>Salary</th>
                  <th>Actions</th>
                </tr>
              </thead>

              <tbody>
                <tr v-for="e in employees" :key="e.id">
                  <td class="employee-name">{{ e.name }}</td>

                  <td>
                    <span class="designation-badge">
                      {{ e.designation }}
                    </span>
                  </td>

                  <td>{{ e.department }}</td>

                  <td class="salary">₹ {{ e.salary }}</td>

                  <td>
                    <button
                      type="button"
                      class="btn btn-warning btn-sm me-2"
                      @click="editEmployee(e)"
                    >
                      Edit
                    </button>

                    <button
                      type="button"
                      class="btn btn-danger btn-sm"
                      @click="deleteEmployee(e.id)"
                    >
                      Delete
                    </button>
                  </td>
                </tr>

                <tr v-if="employees.length === 0">
                  <td colspan="5" class="text-muted py-4">
                    No employee records found
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios"

export default {
  data() {
    return {
      emp: {
        id: "",
        name: "",
        designation: "",
        department: "",
        salary: ""
      },
      employees: [],
      editMode: false,
      message: "",

      // Replace this with your exact MockAPI endpoint
      API_URL: "https://69f89de5f7044aa0103e2b17.mockapi.io/emp/employees"
    }
  },

  mounted() {
    this.getEmployees()
  },

  methods: {
    getEmployees() {
      axios
        .get(this.API_URL)
        .then((res) => {
          this.employees = res.data
        })
        .catch((err) => {
          console.error("GET ERROR:", err)
        })
    },

    saveEmployee() {
      const payload = {
        name: this.emp.name,
        designation: this.emp.designation,
        department: this.emp.department,
        salary: this.emp.salary
      }

      if (this.editMode) {
        axios
          .put(`${this.API_URL}/${this.emp.id}`, payload)
          .then(() => {
            this.message = "Employee updated successfully"
            this.getEmployees()
            this.resetForm()
          })
          .catch((err) => {
            console.error("UPDATE ERROR:", err)
          })
      } else {
        axios
          .post(this.API_URL, payload)
          .then(() => {
            this.message = "Employee added successfully"
            this.getEmployees()
            this.resetForm()
          })
          .catch((err) => {
            console.error("ADD ERROR:", err)
          })
      }

      setTimeout(() => {
        this.message = ""
      }, 3000)
    },

    editEmployee(employee) {
      this.emp = { ...employee }
      this.editMode = true
    },

    deleteEmployee(id) {
      axios
        .delete(`${this.API_URL}/${id}`)
        .then(() => {
          this.message = "Employee deleted successfully"
          this.getEmployees()
        })
        .catch((err) => {
          console.error("DELETE ERROR:", err)
        })

      setTimeout(() => {
        this.message = ""
      }, 3000)
    },

    resetForm() {
      this.emp = {
        id: "",
        name: "",
        designation: "",
        department: "",
        salary: ""
      }

      this.editMode = false
    }
  }
}
</script>