<template>
  <div class="project-detail-page">
    <div class="container">
      <h1>{{ project?.title || 'Project Title' }}</h1>
      <div class="project-meta">
        <span>Status: <strong>{{ project?.status || 'Active' }}</strong></span>
        <span>Created by: <strong>{{ creator?.name || 'Username' }}</strong></span>
      </div>
      <div class="project-description">
        <p>{{ project?.description || 'This is a detailed description of the project. It covers the goals, requirements, and any other relevant information.' }}</p>
      </div>
      <div class="project-skills">
        <h3>Required Skills</h3>
        <ul>
          <li v-for="skill in project?.skills || []" :key="skill">{{ skill }}</li>
        </ul>
      </div>
      <div class="project-actions">
        <button class="btn btn-primary" @click="toggleInterest" v-if="!isOwner">{{ interested ? 'Withdraw Interest' : 'Express Interest' }}</button>
        <button class="btn btn-outline" @click="editProject" v-if="isOwner">Edit Project</button>
        <button class="btn btn-danger" @click="deleteProject" v-if="isOwner">Delete Project</button>
      </div>
      <div class="contributors-section">
        <h3>Contributors</h3>
        <ul>
          <li v-for="contributor in project?.contributors || []" :key="contributor">{{ contributor }}</li>
        </ul>
      </div>
      <div class="discussion-section">
        <h3>Discussion</h3>
        <div class="discussion-thread">
          <div class="comment" v-for="comment in comments" :key="comment.id">
            <strong>{{ comment.author }}:</strong> {{ comment.text }}
          </div>
        </div>
        <textarea v-model="newComment" placeholder="Add a comment..."></textarea>
        <button class="btn btn-primary" @click="addComment" :disabled="addingComment">Post</button>
      </div>
      <div class="kanban-section">
        <h3>Task Board</h3>
        <div class="kanban-board">
          <div class="kanban-column">
            <h4>To-Do</h4>
            <ul>
              <li v-for="task in tasks.todo" :key="task.id">{{ task.text }}</li>
            </ul>
          </div>
          <div class="kanban-column">
            <h4>In Progress</h4>
            <ul>
              <li v-for="task in tasks.inprogress" :key="task.id">{{ task.text }}</li>
            </ul>
          </div>
          <div class="kanban-column">
            <h4>Done</h4>
            <ul>
              <li v-for="task in tasks.done" :key="task.id">{{ task.text }}</li>
            </ul>
          </div>
        </div>
        <div class="add-task">
          <input v-model="newTask.text" placeholder="New task" />
          <select v-model="newTask.status">
            <option value="todo">To-Do</option>
            <option value="inprogress">In Progress</option>
            <option value="done">Done</option>
          </select>
          <button class="btn btn-primary" @click="addTask" :disabled="addingTask">Add Task</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { db, auth } from '../../firebase';
import { doc, getDoc, collection, getDocs, addDoc, serverTimestamp, updateDoc, deleteDoc, arrayUnion, arrayRemove } from 'firebase/firestore';

export default {
  name: 'ProjectDetail',
  data() {
    return {
      project: null,
      creator: null,
      loading: true,
      error: '',
      newComment: '',
      comments: [],
      addingComment: false,
      tasks: { todo: [], inprogress: [], done: [] },
      newTask: { text: '', status: 'todo' },
      addingTask: false,
      userId: null,
      isOwner: false,
      interested: false
    };
  },
  async mounted() {
    if (auth.currentUser) {
      this.userId = auth.currentUser.uid;
    }
    await this.fetchProject();
    await this.fetchComments();
    await this.fetchTasks();
    this.loading = false;
  },
  methods: {
    async fetchProject() {
      try {
        const docRef = doc(db, 'projects', this.$route.params.id);
        const docSnap = await getDoc(docRef);
        if (docSnap.exists()) {
          const data = docSnap.data();
          this.project = { id: docSnap.id, ...data, createdAt: data.createdAt?.toDate ? data.createdAt.toDate() : data.createdAt };
          // Fetch creator info
          if (data.creatorId) {
            const creatorDoc = await getDoc(doc(db, 'users', data.creatorId));
            if (creatorDoc.exists()) {
              const c = creatorDoc.data();
              this.creator = {
                name: c.username || c.email || 'Unknown',
                avatar: c.avatarUrl || 'https://www.gravatar.com/avatar/00000000000000000000000000000000?d=mp&f=y'
              };
            }
          }
          this.isOwner = this.userId && data.creatorId === this.userId;
          // Fetch interest state
          if (this.userId) {
            const userDoc = await getDoc(doc(db, 'users', this.userId));
            if (userDoc.exists()) {
              const interestedProjects = userDoc.data().interestedProjects || [];
              this.interested = interestedProjects.includes(docSnap.id);
            }
          }
        } else {
          this.error = 'Project not found.';
        }
      } catch (err) {
        this.error = 'Failed to load project.';
      }
    },
    async toggleInterest() {
      if (!this.userId) {
        alert('You must be logged in to express interest.');
        return;
      }
      this.interested = !this.interested;
      try {
        const userRef = doc(db, 'users', this.userId);
        if (this.interested) {
          await updateDoc(userRef, {
            interestedProjects: arrayUnion(this.project.id)
          });
        } else {
          await updateDoc(userRef, {
            interestedProjects: arrayRemove(this.project.id)
          });
        }
      } catch (err) {
        alert('Failed to update interest.');
      }
    },
    async fetchComments() {
      try {
        const commentsCol = collection(db, 'projects', this.$route.params.id, 'comments');
        const commentsSnap = await getDocs(commentsCol);
        this.comments = commentsSnap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      } catch (err) {
        this.error = 'Failed to load comments.';
      }
    },
    async addComment() {
      if (!this.newComment.trim()) return;
      this.addingComment = true;
      try {
        await addDoc(collection(db, 'projects', this.project.id, 'comments'), {
          text: this.newComment,
          createdAt: serverTimestamp(),
          author: auth.currentUser?.displayName || auth.currentUser?.email || 'Anonymous',
          userId: auth.currentUser?.uid || null
        });
        this.newComment = '';
        await this.fetchComments();
      } catch (err) {
        this.error = 'Failed to add comment.';
      } finally {
        this.addingComment = false;
      }
    },
    async fetchTasks() {
      try {
        const tasksCol = collection(db, 'projects', this.$route.params.id, 'tasks');
        const tasksSnap = await getDocs(tasksCol);
        const allTasks = tasksSnap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
        this.tasks = { todo: [], inprogress: [], done: [] };
        for (const task of allTasks) {
          if (task.status === 'todo') this.tasks.todo.push(task);
          else if (task.status === 'inprogress') this.tasks.inprogress.push(task);
          else if (task.status === 'done') this.tasks.done.push(task);
        }
      } catch (err) {
        this.error = 'Failed to load tasks.';
      }
    },
    async addTask() {
      if (!this.newTask.text.trim()) return;
      this.addingTask = true;
      try {
        await addDoc(collection(db, 'projects', this.project.id, 'tasks'), {
          text: this.newTask.text,
          status: this.newTask.status,
          createdAt: serverTimestamp()
        });
        await this.fetchTasks();
        this.newTask.text = '';
        this.newTask.status = 'todo';
      } catch (err) {
        this.error = 'Failed to add task.';
      } finally {
        this.addingTask = false;
      }
    },
    async deleteProject() {
      if (!this.isOwner) {
        alert('Only the project owner can delete this project.');
        return;
      }
      if (!confirm('Are you sure you want to delete this project?')) return;
      try {
        await deleteDoc(doc(db, 'projects', this.project.id));
        this.$router.push('/projects');
      } catch (err) {
        this.error = 'Failed to delete project.';
      }
    },
    async editProject() {
      if (!this.isOwner) {
        alert('Only the project owner can edit this project.');
        return;
      }
      this.$router.push(`/projects/${this.project.id}/edit`);
    }
  }
};
</script>

<style scoped>
.project-detail-page { padding: 40px 0; }
.project-meta { margin-bottom: 20px; color: #666; }
.project-skills ul, .contributors-section ul { list-style: disc; margin-left: 20px; }
.project-actions { margin: 20px 0; display: flex; gap: 10px; }
.kanban-board { display: flex; gap: 20px; margin-top: 20px; }
.kanban-column { background: #f4f4f4; border-radius: 8px; padding: 15px; width: 200px; }
.kanban-column h4 { margin-bottom: 10px; }
.discussion-section { margin-top: 40px; }
.discussion-thread { margin-bottom: 10px; }
.comment { background: #f9f8f7; border-radius: 4px; padding: 8px; margin-bottom: 5px; }
.add-task { margin-top: 20px; display: flex; gap: 10px; }
</style>
