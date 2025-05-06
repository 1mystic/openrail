<template>
  <div class="profile-page container">
    <div v-if="loading">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>
      <div class="profile-header">
        <img class="profile-avatar" :src="user.avatarUrl" alt="User Avatar" />
        <div class="profile-info">
          <h1>{{ user.username }}</h1>
          <p>Bio: {{ user.bio }}</p>
          <div class="profile-links">
            <a :href="user.githubUrl" target="_blank">GitHub</a>
            <a :href="user.twitterUrl" target="_blank">Twitter</a>
          </div>
          <button class="btn btn-outline">Edit Profile</button>
        </div>
      </div>
      <div class="profile-projects">
        <h2>My Projects</h2>
        <ul>
          <li v-for="project in user.projects" :key="project">{{ project }}</li>
        </ul>
        <h2>Contributed Projects</h2>
        <ul>
          <li v-for="project in user.contributedProjects" :key="project">{{ project }}</li>
        </ul>
        <h2>Interested Projects</h2>
        <ul>
          <li v-for="project in user.interestedProjects" :key="project">{{ project }}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import { db } from '../../firebase';
import { doc, getDoc, query, collection, where, getDocs } from 'firebase/firestore';
import { auth } from '../../firebase';

export default {
  name: 'Profile',
  data() {
    return {
      user: null,
      loading: true,
      error: ''
    };
  },
  async mounted() {
    try {
      let uid = null;
      // Always use auth.currentUser.uid for the logged-in user
      if (auth.currentUser) {
        uid = auth.currentUser.uid;
      }
      if (!uid) {
        this.error = 'User not found.';
        this.loading = false;
        return;
      }
      const userDoc = await getDoc(doc(db, 'users', uid));
      if (userDoc.exists()) {
        this.user = userDoc.data();
      } else {
        this.error = 'User not found.';
      }
    } catch (err) {
      this.error = 'Failed to load profile.';
    } finally {
      this.loading = false;
    }
  }
};
</script>

<style scoped>
.profile-header { display: flex; align-items: center; gap: 30px; margin: 40px 0; }
.profile-avatar { width: 120px; height: 120px; border-radius: 50%; border: 4px solid #375bb1; }
.profile-info h1 { margin-bottom: 10px; }
.profile-links a { margin-right: 10px; color: #375bb1; }
.profile-projects { margin-top: 40px; }
.profile-projects h2 { margin-top: 30px; }
</style>
