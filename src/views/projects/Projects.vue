<template>
  <div class="projects-page container">
    <div class="projects-header">
      <h1>Discover Projects</h1>
      <div class="projects-controls">
        <input v-model="searchQuery" @input="filterProjects" type="text" placeholder="Search projects..." class="search-input" />
        <button class="btn btn-outline sort-button" @click.stop="toggleSortMenu">Sort</button>
        <button class="btn btn-outline" @click.stop="toggleFilters">Filters</button>
        <router-link to="/projects/create" class="btn btn-primary">Create Project</router-link>
        <div v-if="showSortMenu" class="sort-menu">
          <div v-for="(option, key) in sortOptions" :key="key" @click="selectSort(key)" class="sort-option">
            {{ option.label }}
          </div>
        </div>
        <div v-if="showFilters" class="filter-dropdown">
          <div class="filter-section">
            <label>Status:</label>
            <div>
              <label><input type="checkbox" v-model="filters.status.active" @change="applyFilters" /> Active</label>
              <label><input type="checkbox" v-model="filters.status.completed" @change="applyFilters" /> Completed</label>
            </div>
          </div>
          <div class="filter-section">
            <label>Skills:</label>
            <div class="skills-list">
              <label v-for="skill in availableSkills" :key="skill">
                <input type="checkbox" :value="skill" v-model="filters.skills" @change="applyFilters" /> {{ skill }}
              </label>
            </div>
          </div>
          <button class="btn btn-outline" @click="resetFilters">Reset</button>
        </div>
      </div>
    </div>
    <div v-if="loading" class="loading">Loading projects...</div>
    <div v-else>
      <div v-if="filteredProjects.length === 0" class="no-projects">No projects found.</div>
      <div class="projects-list">
        <div v-for="project in filteredProjects" :key="project.id" class="project-card">
          <div class="project-header">
            <img :src="project.creator.avatar" class="creator-avatar" :alt="project.creator.name" />
            <div>
              <h2>{{ project.title }}</h2>
              <div class="project-meta">
                <span>Status: <strong>{{ project.status }}</strong></span>
                <span>By: <strong>{{ project.isOwner ? 'My project' : project.creator.name }}</strong></span>
                <span>Views: {{ project.views }}</span>
                <span>Collaborators: {{ project.collaborators }}</span>
              </div>
            </div>
          </div>
          <p class="project-description">{{ project.description }}</p>
          <div class="project-skills">
            <span v-for="skill in project.skills" :key="skill" class="skill-tag">{{ skill }}</span>
          </div>
          <div class="project-actions">
            <router-link :to="`/projects/${project.id}`" class="btn btn-outline">View Details</router-link>
            <button v-if="project.showInterest" class="btn btn-primary" :class="{ interested: project.interested }" @click="toggleInterest(project)">
              {{ project.interested ? 'Interested' : 'Express Interest' }}
            </button>
            <button v-if="project.isOwner" class="btn btn-danger" @click="deleteProject(project.id)">Delete</button>
          </div>
        </div>
      </div>
      <div v-if="filteredProjects.length > 0" class="load-more-section">
        <button class="btn btn-outline" @click="loadMore" :disabled="loadingMore">
          {{ loadingMore ? 'Loading...' : 'Load More' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { db, auth } from '../../firebase';
import { collection, getDocs, query, orderBy, doc, updateDoc, arrayUnion, arrayRemove, deleteDoc, getDoc } from 'firebase/firestore';

export default {
  name: 'Projects',
  data() {
    return {
      projects: [],
      filteredProjects: [],
      searchQuery: '',
      showFilters: false,
      showSortMenu: false,
      currentSort: 'newest',
      sortOptions: {
        newest: {
          label: 'Newest',
          sort: (a, b) => new Date(b.createdAt?.toDate?.() || b.createdAt) - new Date(a.createdAt?.toDate?.() || a.createdAt)
        },
        oldest: {
          label: 'Oldest',
          sort: (a, b) => new Date(a.createdAt?.toDate?.() || a.createdAt) - new Date(b.createdAt?.toDate?.() || b.createdAt)
        },
        mostViews: {
          label: 'Most Views',
          sort: (a, b) => b.views - a.views
        },
        mostCollaborators: {
          label: 'Most Collaborators',
          sort: (a, b) => b.collaborators - a.collaborators
        }
      },
      filters: {
        status: {
          active: true,
          completed: true
        },
        skills: []
      },
      loading: true,
      loadingMore: false,
      error: '',
      userInterestedProjects: [],
      userId: null
    };
  },
  async mounted() {
    if (auth.currentUser) {
      this.userId = auth.currentUser.uid;
      // Fetch user's interested projects
      const userDoc = await getDoc(doc(db, 'users', this.userId));
      if (userDoc.exists()) {
        this.userInterestedProjects = userDoc.data().interestedProjects || [];
      }
    }
    await this.fetchProjects();
    document.addEventListener('click', this.handleClickOutside);
  },
  beforeUnmount() {
    document.removeEventListener('click', this.handleClickOutside);
  },
  computed: {
    availableSkills() {
      const allSkills = this.projects.flatMap(project => project.skills || []);
      return [...new Set(allSkills)].sort();
    }
  },
  methods: {
    async fetchProjects() {
      this.loading = true;
      try {
        const q = query(collection(db, 'projects'), orderBy('createdAt', 'desc'));
        const querySnapshot = await getDocs(q);
        const projects = [];
        for (const docSnap of querySnapshot.docs) {
          const data = docSnap.data();
          let creator = { name: 'Unknown', avatar: 'https://www.gravatar.com/avatar/00000000000000000000000000000000?d=mp&f=y' };
          if (data.creatorId) {
            const creatorDoc = await getDoc(doc(db, 'users', data.creatorId));
            if (creatorDoc.exists()) {
              const c = creatorDoc.data();
              creator = {
                name: c.username || c.email || 'Unknown',
                avatar: c.avatarUrl || 'https://www.gravatar.com/avatar/00000000000000000000000000000000?d=mp&f=y'
              };
            }
          }
          projects.push({
            id: docSnap.id,
            ...data,
            createdAt: data.createdAt?.toDate ? data.createdAt.toDate() : data.createdAt,
            isOwner: this.userId && data.creatorId === this.userId,
            showInterest: this.userId && data.creatorId !== this.userId,
            interested: this.userInterestedProjects.includes(docSnap.id),
            creator
          });
        }
        this.projects = projects;
        this.filterProjects();
      } catch (err) {
        this.error = 'Failed to load projects.';
      } finally {
        this.loading = false;
      }
    },
    async deleteProject(projectId) {
      const project = this.projects.find(p => p.id === projectId);
      if (!project || !project.isOwner) {
        alert('Only the project owner can delete this project.');
        return;
      }
      if (!confirm('Are you sure you want to delete this project?')) return;
      try {
        await deleteDoc(doc(db, 'projects', projectId));
        await this.fetchProjects();
      } catch (err) {
        alert('Failed to delete project.');
      }
    },
    filterProjects() {
      let results = [...this.projects];

      // Filter by search query
      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase();
        results = results.filter(project =>
          project.title.toLowerCase().includes(query) ||
          project.description.toLowerCase().includes(query)
        );
      }

      // Filter by status
      results = results.filter(project => {
        if (project.status === 'Active' && this.filters.status.active) return true;
        if (project.status === 'Completed' && this.filters.status.completed) return true;
        return false;
      });

      // Filter by skills
      if (this.filters.skills.length > 0) {
        results = results.filter(project =>
          project.skills.some(skill => this.filters.skills.includes(skill))
        );
      }

      // Apply sorting
      results.sort(this.sortOptions[this.currentSort].sort);

      this.filteredProjects = results;
    },
    toggleFilters(event) {
      // Prevent event propagation to avoid closing immediately
      if (event) event.stopPropagation();
      this.showFilters = !this.showFilters;
      // Close sort menu if open
      this.showSortMenu = false;
    },
    toggleSortMenu(event) {
      // Prevent event propagation to avoid closing immediately
      if (event) event.stopPropagation();
      this.showSortMenu = !this.showSortMenu;
      // Close filters if open
      this.showFilters = false;
    },
    handleClickOutside(event) {
      const filterDropdown = document.querySelector('.filter-dropdown');
      const sortButton = document.querySelector('.sort-button');

      // Check if the click is outside the filter dropdown
      if (filterDropdown && !filterDropdown.contains(event.target)) {
        this.showFilters = false;
      }

      // Check if the click is outside the sort menu
      if (sortButton && !sortButton.contains(event.target) && !event.target.closest('.sort-menu')) {
        this.showSortMenu = false;
      }
    },
    resetFilters() {
      this.filters = {
        status: {
          active: true,
          completed: true
        },
        skills: []
      };
      this.searchQuery = '';
      this.filterProjects();
    },
    applyFilters() {
      this.filterProjects();
      this.showFilters = false;
    },
    selectSort(sortKey) {
      this.currentSort = sortKey;
      this.showSortMenu = false;
      this.filterProjects();
    },
    async toggleInterest(project) {
      if (!this.userId) {
        alert('You must be logged in to express interest.');
        return;
      }
      project.interested = !project.interested;
      try {
        const userRef = doc(db, 'users', this.userId);
        if (project.interested) {
          await updateDoc(userRef, {
            interestedProjects: arrayUnion(project.id)
          });
        } else {
          await updateDoc(userRef, {
            interestedProjects: arrayRemove(project.id)
          });
        }
        // Update local state
        if (project.interested) {
          this.userInterestedProjects.push(project.id);
        } else {
          this.userInterestedProjects = this.userInterestedProjects.filter(id => id !== project.id);
        }
      } catch (err) {
        alert('Failed to update interest.');
      }
    },
    loadMore() {
      this.loadingMore = true;

      // Simulate loading more data
      setTimeout(() => {
        this.loadingMore = false;

        // In a real app, you would fetch more projects from an API
        // For demo purposes, we'll just notify the user
        alert('In a real app, more projects would be loaded here.');
      }, 1000);
    }
  }
};
</script>

<style scoped>
.projects-page { padding: 40px 0; }
.projects-header { display: flex; flex-direction: column; gap: 20px; margin-bottom: 30px; }
.projects-controls { display: flex; gap: 10px; align-items: center; position: relative; }
.search-input { padding: 8px; border-radius: 4px; border: 1px solid #ccc; width: 200px; }
.sort-menu, .filter-dropdown { position: absolute; top: 40px; left: 120px; background: #fff; border: 1px solid #ddd; border-radius: 6px; z-index: 10; padding: 10px; min-width: 150px; box-shadow: 0 2px 8px rgba(0,0,0,0.08); }
.sort-menu { left: 220px; }
.sort-option { padding: 6px 10px; cursor: pointer; border-radius: 4px; }
.sort-option:hover { background: #f4f4f4; }
.filter-section { margin-bottom: 10px; }
.skills-list { display: flex; flex-wrap: wrap; gap: 8px; }
.no-projects { text-align: center; color: #888; margin: 40px 0; }
.projects-list { display: grid; grid-template-columns: repeat(auto-fit, minmax(340px, 1fr)); gap: 24px; }
.project-card { background: #f9f9f9; border-radius: 10px; padding: 24px; box-shadow: 0 2px 8px rgba(0,0,0,0.04); display: flex; flex-direction: column; gap: 12px; }
.project-header { display: flex; gap: 16px; align-items: center; }
.creator-avatar { width: 48px; height: 48px; border-radius: 50%; border: 2px solid #375bb1; }
.project-meta { font-size: 0.95em; color: #666; display: flex; gap: 16px; flex-wrap: wrap; }
.project-description { margin: 10px 0; color: #444; }
.project-skills { display: flex; gap: 8px; flex-wrap: wrap; }
.skill-tag { background: #e3e9f7; color: #375bb1; border-radius: 4px; padding: 2px 8px; font-size: 0.92em; }
.project-actions { display: flex; gap: 10px; margin-top: 8px; }
.btn.interested { background: #375bb1; color: #fff; border: none; }
.load-more-section { text-align: center; margin: 30px 0 0; }
.loading { text-align: center; color: #888; margin: 40px 0; }
</style>
