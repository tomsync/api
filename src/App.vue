<template>
  <div>
    <h1>User List</h1>

    <!-- Input form สำหรับเพิ่มผู้ใช้ใหม่ -->
    <div>
      <input v-model="newUser.name" placeholder="Enter name" />
      <button @click="addUser">Submit</button>
    </div>

    <ul>
      <li v-for="user in users" :key="user.id" class="user-item">
        <span class="user-id">ID: {{ user.id }}</span>
        <span class="user-name">Name: {{ user.name }}</span>

        <!-- Edit and Delete Buttons -->
        <button @click="editUser(user)">Edit</button>
        <button @click="deleteUser(user.id)">Delete</button>
      </li>
    </ul>

    <!-- Edit User Form -->
    <div v-if="editingUser">
      <h2>Edit User</h2>
      <input v-model="editingUser.name" placeholder="Edit name" />
      <button @click="updateUser">Update</button>
      <button @click="cancelEdit">Cancel</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      users: [],
      newUser: {
        name: "",
      },
      editingUser: null, // To track the user being edited
    };
  },
  mounted() {
    this.fetchUsers();
  },
  methods: {
    fetchUsers() {
      axios
        .get("https://localhost:7263/api/User")
        .then((response) => {
          this.users = response.data;
        })
        .catch((error) => {
          console.error("Error fetching data:", error);
        });
    },
    addUser() {
      if (this.newUser.name.trim() === "") {
        alert("กรุณากรอกชื่อผู้ใช้");
        return;
      }

      axios
        .post("https://localhost:7263/api/User", this.newUser)
        .then((response) => {
          console.log("User added successfully:", response.data);
          this.users.push(response.data);
          this.newUser.name = "";
        })
        .catch((error) => {
          console.error("Error adding user:", error);
        });
    },
    editUser(user) {
      this.editingUser = { ...user }; // Copy the user data to editingUser
    },
    //-------------------- put ----------------------------------------------//
    updateUser() {
      if (!this.editingUser.name.trim()) {
        alert("กรุณากรอกชื่อผู้ใช้");
        return;
      }

      console.log("Editing user:", this.editingUser);

      axios
        .put(
          `https://localhost:7263/api/User/${this.editingUser.id}`,
          this.editingUser
        )
        .then((response) => {
          console.log("User updated successfully:", response.data);
          const index = this.users.findIndex(
            (u) => u.id === this.editingUser.id
          );
          if (index !== -1) {
            this.users[index] = response.data; // Update the user in the list
          }
          this.editingUser = null; // Clear the edit form
        })
        .catch((error) => {
          console.error(
            "Error updating user:",
            error.response ? error.response.data : error.message
          );
        });
    },
    //------------------ delete ---------------------------------
    deleteUser(id) {
      if (confirm("คุณแน่ใจหรือว่าต้องการลบผู้ใช้นี้?")) {
        axios
          .delete(`https://localhost:7263/api/User/${id}`)
          .then(() => {
            console.log("User deleted successfully");
            this.users = this.users.filter((user) => user.id !== id); // Remove the user from the list
          })
          .catch((error) => {
            console.error(
              "Error deleting user:",
              error.response ? error.response.data : error.message
            );
          });
      }
    },
    cancelEdit() {
      this.editingUser = null; // Cancel the edit
    },
  },
};
</script>


 <style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.user-item {
  padding: 8px;
  border-bottom: 1px solid #ddd;
}

.user-id {
  font-weight: bold;
  padding-right: 300px;
}

.user-name {
  color: #333;
}

input {
  padding: 8px;
  margin-right: 10px;
}

button {
  padding: 8px;
  background-color: #4898b8;
  color: white;
  border: none;
  cursor: pointer;
  margin-left: 20px;
}

button:hover {
  background-color: #1b91bd;
}
</style>