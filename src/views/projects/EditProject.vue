<template>
  <div class="edit-project-page container">
    <h1>Edit Project</h1>
    <form class="project-form" @submit="handleSubmit">
      <div v-if="isLoading">Loading...</div>
      <div v-if="error" class="error">{{ error }}</div>
      <div class="form-group">
        <label>Title</label>
        <input type="text" v-model="title" />
      </div>
      <div class="form-group">
        <label>Description</label>
        <textarea v-model="description"></textarea>
      </div>
      <div class="form-group">
        <label>Status</label>
        <select v-model="status">
          <option>Active</option>
          <option>Completed</option>
        </select>
      </div>
      <div class="form-group">
        <label>GitHub Repo URL</label>
        <input type="url" v-model="githubUrl" />
      </div>
      <div class="form-group">
        <label>Required Skills</label>
        <input type="text" v-model="skills" />
      </div>
      <button class="btn btn-primary" type="submit" :disabled="isLoading">Save Changes</button>
    </form>
  </div>
</template>

<script>
import { db } from '../../firebase';
import { doc, getDoc, updateDoc } from 'firebase/firestore';

export default {
  name: 'EditProject',
  data() {
    return {
      id: this.$route.params.id,
      title: '',
      description: '',
      status: 'Active',
      githubUrl: '',
      skills: '',
      isLoading: false,
      error: ''
    };
  },
  async mounted() {
    // Fetch project data from Firestore
    const docRef = doc(db, 'projects', this.id);
    const docSnap = await getDoc(docRef);
    if (docSnap.exists()) {
      const data = docSnap.data();
      this.title = data.title;
      this.description = data.description;
      this.status = data.status;
      this.githubUrl = data.githubUrl;
      this.skills = data.skills ? data.skills.join(', ') : '';
    } else {
      this.error = 'Project not found.';
    }
  },
  methods: {
    async handleSubmit(e) {
      e.preventDefault();
      this.isLoading = true;
      this.error = '';
      try {
        const docRef = doc(db, 'projects', this.id);
        await updateDoc(docRef, {
          title: this.title,
          description: this.description,
          status: this.status,
          githubUrl: this.githubUrl,
          skills: this.skills.split(',').map(s => s.trim())
        });
        this.$router.push('/projects/' + this.id);
      } catch (err) {
        this.error = 'Failed to update project.';
      } finally {
        this.isLoading = false;
      }
    }
  }
};
</script>

<style scoped>
.edit-project-page { padding: 40px 0; }
.project-form { max-width: 500px; margin: 0 auto; }
.form-group { margin-bottom: 20px; }
label { font-weight: 600; display: block; margin-bottom: 8px; }
input, textarea, select { width: 100%; padding: 10px; border-radius: 4px; border: 1px solid #ccc; }
.btn-primary { margin-top: 10px; }
.error { color: red; margin-bottom: 20px; }
</style>
