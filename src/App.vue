<template>
  <div class="container">
    <div class="cont-input">
      <h1>Reminder</h1>
      <input type="text" v-model="newActivity" placeholder="Waktu Sholat">
      <br><br>
      <input type="text" v-model="newActivitySurat" placeholder="Surat">
      <br><br>
      <input type="date" v-model="newActivityDate">
      <br><br>
      <input type="time" v-model="newActivityTime">
      <br><br>
      <button @click="addOrUpdateActivity">{{ editingIndex === null ? 'Tambah' : 'Perbarui' }}</button>
    </div>
    
    <div class="cont-isi">
      <input type="text" v-model="searchQuery" placeholder="Cari kegiatan..."> 
      <br><br>
      <div class="dropdown">
        <button class="dropbtn" @click="toggleDropdown">
          Daily Routine <span class="arrow">&#9660;</span>
        </button>
        <div class="dropdown-content">
          <button @click="setFilter('all')" :class="{ 'active': filter === 'all' }">Semua Kegiatan</button>
          <button @click="setFilter('incomplete')" :class="{ 'active': filter === 'incomplete' }">Kegiatan Belum Selesai</button>
          <button @click="setFilter('completed')" :class="{ 'active': filter === 'completed' }">Kegiatan Selesai</button>
        </div>
      </div>
      <br><br>

      <table class="activity-table">
        <thead>
          <tr>
            <th>Waktu Sholat</th>
            <th>Surat</th>
            <th>Tanggal</th>
            <th>Jam</th>
            <th>Status</th>
            <th>Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(activity, index) in filteredActivities" :key="index">
            <td>
              <input type="checkbox" v-model="activity.completed">
              <span :class="{ 'completed': activity.completed }">{{ activity.newActivity }}</span>
            </td>
            <td>
              <span :class="{ 'completed': activity.completed }">{{ activity.newActivitySurat }}</span>
            </td>
            <td>
              <span :class="{ 'selesai': activity.completed }">{{ activity.newActivityDate }}</span>
            </td>
            <td>
              <span :class="{ 'selesai': activity.completed }">{{ activity.newActivityTime }}</span>
            </td>
            <td>
              <span :class="{ 'selesai': activity.completed }">{{ activity.completed ? 'Selesai' : 'Belum Selesai' }}</span>
            </td>
            <td>
              <button @click="editActivity(index)" class="edit-button">Edit</button>
              <button @click="cancelActivity(index)" class="cancel-button">Hapus</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      activities: [],
      newActivity: '',
      newActivitySurat: '',
      newActivityDate: '',
      newActivityTime: '',
      searchQuery: '',
      filter: 'all',
      editingIndex: null // Track the index of the activity being edited
    };
  },
  computed: {
    filteredActivities() {
      let filtered = this.activities;

      // Filter based on search query
      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase();
        filtered = filtered.filter(activity => {
          return (
            activity.newActivity.toLowerCase().includes(query) ||
            activity.newActivitySurat.toLowerCase().includes(query) ||
            activity.newActivityDate.toLowerCase().includes(query) ||
            activity.newActivityTime.toLowerCase().includes(query)
          );
        });
      }

      // Apply status filter
      if (this.filter === 'incomplete') {
        filtered = filtered.filter(activity => !activity.completed);
      } else if (this.filter === 'completed') {
        filtered = filtered.filter(activity => activity.completed);
      }

      return filtered;
    }
  },
  methods: {
    addOrUpdateActivity() {
      if (this.editingIndex !== null) {
        // Update existing activity
        const editedActivity = {
          newActivity: this.newActivity,
          newActivitySurat: this.newActivitySurat,
          newActivityDate: this.newActivityDate,
          newActivityTime: this.newActivityTime,
          completed: this.activities[this.editingIndex].completed
        };
        this.activities.splice(this.editingIndex, 1, editedActivity);
        this.editingIndex = null; // Reset editing state
      } else {
        // Add new activity
        if (this.newActivity.trim() !== '' && this.newActivitySurat.trim() !== '') {
          this.activities.push({
            newActivity: this.newActivity,
            newActivitySurat: this.newActivitySurat,
            newActivityDate: this.newActivityDate,
            newActivityTime: this.newActivityTime,
            completed: false,
          });
        }
      }

      // Reset form fields
      this.newActivity = '';
      this.newActivitySurat = '';
      this.newActivityDate = '';
      this.newActivityTime = '';
    },
    cancelActivity(index) {
      this.activities.splice(index, 1);
    },
    setFilter(filter) {
      this.filter = filter;
    },
    editActivity(index) {
      const activity = this.activities[index];
      // Set the form fields to the values of the selected activity for editing
      this.newActivity = activity.newActivity;
      this.newActivitySurat = activity.newActivitySurat;
      this.newActivityDate = activity.newActivityDate;
      this.newActivityTime = activity.newActivityTime;
      this.editingIndex = index; // Set the editing index
    }
  }
};
</script>

<style scoped>
/* Add your CSS styles here */
</style>
