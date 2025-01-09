<template>
    <div class="user-management">
      <h1>User Management</h1>
      <button @click="openModal()">Add User</button>
      <UserList :users="users" @edit-user="openModal" @delete-request="handleDeleteRequest" />
      <ConfirmDeleteModal
        :isVisible="isDeleteModalVisible"
        :userId="userToDelete"
        @confirm="deleteUser"
        @cancel="closeDeleteModal"
      />

      <div v-if="isModalOpen" class="modal">
        <div class="modal-content">
          <h2>{{ isEditMode ? 'Edit User' : 'Add User' }}</h2>
          <form @submit.prevent="saveUser">
            <div>
              <label for="user_email">Email: </label>
              <input type="email" v-model="userForm.user_email" required />
            </div>
            <div>
              <label for="first_name">First Name: </label>
              <input type="text" v-model="userForm.first_name" required />
            </div>
            <div>
              <label for="last_name">Last Name: </label>
              <input type="text" v-model="userForm.last_name" required />
            </div>
            <button type="submit">{{ isEditMode ? 'Update' : 'Add' }}</button>
            <button type="button" @click="closeModal">Cancel</button>
          </form>
        </div>  
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  import UserList from '../components/UserList.vue';
  import ConfirmDeleteModal from '../components/ConfirmDeleteModal.vue';
  
  const users = ref([]);
  const isModalOpen = ref(false);
  const isEditMode = ref(false);
  const isDeleteModalVisible = ref(false);
  const userToDelete = ref(null);
  const userForm = ref({
    id: null,
    user_email: '',
    first_name: '',
    last_name: ''
  });
  
  const fetchUsers = async () => {

    try {
      const response = await axios.get('http://localhost:8000/api/users');
      users.value = response.data['hydra:member'];
    } catch (error) {
      console.error('Error fetching users:', error);
    }
  };

  const openModal = (user = null) => {
    if (user) {
      isEditMode.value = true;
      userForm.value = { ...user };
    } else {
      isEditMode.value = false;
      userForm.value = {
        id: null,
        user_email: '',
        first_name: '',
        last_name: ''
      };
    }
    isModalOpen.value = true;
  };

  const closeModal = () => {
    isModalOpen.value = false;
  };

  const saveUser = async () => {
    const headers = { 'Content-Type': 'application/ld+json' };

    try {
      const userData = {
        
        user_email: userForm.value.user_email,
        first_name: userForm.value.first_name,
        last_name: userForm.value.last_name,
      };

      if (userForm.value.id) {
        // Editing existing user
        await axios.put(`http://localhost:8000/api/users/${userForm.value.id}`, userData, { headers });
        
      } else {
        // Adding new user
        await axios.post('http://localhost:8000/api/users', userData, { headers });
      }
      await fetchUsers(); // Refresh the list of users
      isModalOpen.value = false;
    } catch (error) {
      console.error('Error saving user:', error);
    }
  };

  const handleDeleteRequest = (userId) => {
    userToDelete.value = userId;
    isDeleteModalVisible.value = true;
  }

  const deleteUser = async (userId) => {
    try {
      await axios.delete(`http://localhost:8000/api/users/${userId}`);
      users.value = users.value.filter(user => user.id !== userId);
      //closeDeleteModal();
      isDeleteModalVisible.value = false;
    } catch (error) {
      console.error('Error deleting user:', error);
    }
  };

  const closeDeleteModal = () => {
    isDeleteModalVisible.value = false;
  };
  
  onMounted(fetchUsers);
  </script>
  
  <style scoped>
  

  </style>