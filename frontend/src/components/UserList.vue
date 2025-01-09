<template>
    <div class="user-list">
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th @click="sortByEmail('user_email')" style='cursor:pointer;'>Email</th>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Created At</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in sortedUsers" :key="user.id">
            <td>{{ user.id }}</td>
            <td>{{ user.user_email }}</td>
            <td>{{ user.first_name }}</td>
            <td>{{ user.last_name }}</td>
            <td>{{ user.created_at }}</td>
            <td>
              <button @click="editUser(user)">Edit</button>
              <button @click="$emit('delete-request', user.id)" class="delete">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script setup>
  import { defineEmits, defineProps, ref, computed, onMounted } from 'vue';
  
  const props = defineProps({
    users: {
        type: Array,
        required: true,
    },
  });

  const sortKey = ref('id');
  const sortOrder = ref('asc');

  // Load sorting preferences from localStorage
  onMounted(() => {
    const savedSortKey = localStorage.getItem('sortKey');
    const savedSortOrder = localStorage.getItem('sortOrder');
    if (savedSortKey) sortKey.value = savedSortKey;
    if (savedSortOrder) sortOrder.value = savedSortOrder;
  });

  const sortedUsers = computed(() => {
    return [...props.users].sort((a, b) => {
      if (a[sortKey.value] < b[sortKey.value]) return sortOrder.value === 'asc' ? -1 : 1;
      if (a[sortKey.value] > b[sortKey.value]) return sortOrder.value === 'asc' ? 1 : -1;
    });
  });

  const sortByEmail = (key) => {
    if (sortKey.value === key) {
      sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc';
    } else {
      sortKey.value = key;
      sortOrder.value = 'asc';
    }
    // Save sorting preferences to localStorage
    localStorage.setItem('sortKey', sortKey.value);
    localStorage.setItem('sortOrder', sortOrder.value)
  };
  
  const emit = defineEmits(['edit-user']);
  
  const editUser = (user) => {
    emit('edit-user', user);
  };
  
  </script>
  
  <style scoped>

  </style>