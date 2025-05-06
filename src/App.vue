<template>
  <div id="app" :class="{ 'dark-mode': darkMode }">
    <header class="header">
      <div class="container">
        <div class="header-content">
          <div class="logo">
            <router-link to="/">
              <svg width="96" height="24" viewBox="0 0 96 24" xmlns="http://www.w3.org/2000/svg">
                <path d="M12 0H0V24H12C18.627 24 24 18.627 24 12C24 5.373 18.627 0 12 0ZM12 16C7.582 16 4 13.314 4 10C4 6.686 7.582 4 12 4C16.418 4 20 6.686 20 10C20 13.314 16.418 16 12 16Z" fill="#375bb1"/>
                <path d="M39.001 4.72H33.353V18.16H36.305V14.096H39.001C42.193 14.096 44.209 12.224 44.209 9.392C44.209 6.56 42.193 4.72 39.001 4.72ZM38.785 11.552H36.305V7.264H38.785C40.225 7.264 41.161 8.048 41.161 9.392C41.161 10.736 40.225 11.552 38.785 11.552Z" fill="#333333"/>
                <path d="M51.619 18.352C54.875 18.352 57.091 16.064 57.091 12.784C57.091 9.504 54.875 7.216 51.619 7.216C48.363 7.216 46.147 9.504 46.147 12.784C46.147 16.064 48.363 18.352 51.619 18.352ZM51.619 15.776C50.083 15.776 49.051 14.576 49.051 12.784C49.051 10.992 50.083 9.792 51.619 9.792C53.155 9.792 54.187 10.992 54.187 12.784C54.187 14.576 53.155 15.776 51.619 15.776Z" fill="#333333"/>
                <path d="M65.859 18.352C69.115 18.352 71.331 16.064 71.331 12.784C71.331 9.504 69.115 7.216 65.859 7.216C62.603 7.216 60.387 9.504 60.387 12.784C60.387 16.064 62.603 18.352 65.859 18.352ZM65.859 15.776C64.323 15.776 63.291 14.576 63.291 12.784C63.291 10.992 64.323 9.792 65.859 9.792C67.395 9.792 68.427 10.992 68.427 12.784C68.427 14.576 67.395 15.776 65.859 15.776Z" fill="#333333"/>
                <path d="M79.294 7.216C77.086 7.216 75.038 8.272 74.262 10.16L76.87 11.104C77.214 10.192 78.102 9.632 79.294 9.632C80.67 9.632 81.478 10.32 81.478 11.488H79.358C76.454 11.488 74.694 12.64 74.694 14.72C74.694 16.8 76.358 18.16 78.854 18.16C80.29 18.16 81.254 17.68 81.894 16.672V17.952H84.494V11.744C84.494 8.88 82.526 7.216 79.294 7.216ZM79.73 15.936C78.534 15.936 77.694 15.36 77.694 14.448C77.694 13.536 78.438 13.008 79.73 13.008H81.478V13.856C81.478 15.072 80.798 15.936 79.73 15.936Z" fill="#333333"/>
                <path d="M94.2699 7.408H91.2219V5.2H88.2699V7.408H86.2219V9.856H88.2699V14.864C88.2699 17.152 89.5899 18.256 93.0219 17.952V15.648C91.5339 15.728 91.2219 15.488 91.2219 14.864V9.856H94.2699V7.408Z" fill="#333333"/>
              </svg>
            </router-link>
          </div>
          <nav class="main-nav">
            <router-link to="/projects" class="nav-link">Projects</router-link>
            <router-link to="/community" class="nav-link">Community</router-link>
            <router-link to="/mentorship" class="nav-link">Mentorship</router-link>
            <router-link to="/hackathons" class="nav-link">Hackathons</router-link>
            <router-link to="/about" class="nav-link">About</router-link>
          </nav>
          <div class="nav-actions">
            <button @click="toggleDarkMode" class="theme-toggle">
              <span v-if="darkMode" class="light-icon">☀️</span>
              <span v-else class="dark-icon">🌙</span>
            </button>
            <template v-if="isLoggedIn">
              <div class="user-menu" @click="toggleUserMenu">
                <img :src="userAvatar" alt="User Avatar" class="user-avatar">
                <div class="user-menu-dropdown" v-if="showUserMenu">
                  <router-link to="/profile/username" class="dropdown-item">My Profile</router-link>
                  <router-link to="/profile/username/portfolio" class="dropdown-item">Portfolio</router-link>
                  <router-link to="/projects/create" class="dropdown-item">Create Project</router-link>
                  <div class="dropdown-divider"/>
                  <button @click="handleLogout" class="dropdown-item logout-btn">Log Out</button>
                </div>
              </div>
            </template>
            <template v-else>
              <router-link to="/login" class="login-btn">Log In</router-link>
              <router-link to="/signup" class="signup-btn">Sign Up</router-link>
            </template>
          </div>
          <button class="menu-toggle" @click="toggleMobileMenu">
            <span class="menu-icon"/>
          </button>
        </div>
      </div>
      <!-- Mobile menu -->
      <div class="mobile-menu" v-if="showMobileMenu">
        <router-link to="/projects" class="mobile-nav-link">Projects</router-link>
        <router-link to="/community" class="mobile-nav-link">Community</router-link>
        <router-link to="/mentorship" class="mobile-nav-link">Mentorship</router-link>
        <router-link to="/hackathons" class="mobile-nav-link">Hackathons</router-link>
        <router-link to="/about" class="mobile-nav-link">About</router-link>
        <template v-if="isLoggedIn">
          <div class="mobile-divider"/>
          <router-link to="/profile/username" class="mobile-nav-link">My Profile</router-link>
          <router-link to="/profile/username/portfolio" class="mobile-nav-link">Portfolio</router-link>
          <router-link to="/projects/create" class="mobile-nav-link">Create Project</router-link>
          <button @click="handleLogout" class="mobile-nav-link logout-btn">Log Out</button>
        </template>
        <template v-else>
          <div class="mobile-divider"/>
          <router-link to="/login" class="mobile-nav-link">Log In</router-link>
          <router-link to="/signup" class="mobile-nav-link">Sign Up</router-link>
        </template>
      </div>
    </header>

    <main>
      <router-view/>
    </main>
    <AiAssistPane v-if="showAiPane" :isLoggedIn="isLoggedIn" />
    <button class="ai-fab" @click="toggleAiPane">
      <span v-if="showAiPane">✖</span>
      <span v-else>🤖</span>
    </button>
    <footer class="footer">
      <div class="container">
        <div class="footer-content">
          <div class="footer-statement">
            <p>Building great projects together.</p>
            <div class="social-links">
              <a href="https://t.me/profilaunch" class="social-link" title="Telegram">
                <span class="social-icon telegram"/>
              </a>
              <a href="https://twitter.com/profilaunch" class="social-link" title="Twitter">
                <span class="social-icon twitter"/>
              </a>
              <a href="https://discord.gg/profilaunch" class="social-link" title="Discord">
                <span class="social-icon discord"/>
              </a>
              <a href="https://github.com/profilaunch" class="social-link" title="Github">
                <span class="social-icon github"/>
              </a>
              <a href="https://www.linkedin.com/company/profilaunch/" class="social-link" title="LinkedIn">
                <span class="social-icon linkedin"/>
              </a>
            </div>
          </div>
          <div class="footer-links">
            <div class="footer-section">
              <h3>Features</h3>
              <ul>
                <li><router-link to="/projects">Browse Projects</router-link></li>
                <li><router-link to="/projects/create">Create Project</router-link></li>
                <li><router-link to="/community">Community</router-link></li>
                <li><router-link to="/mentorship">Mentorship</router-link></li>
              </ul>
            </div>
            <div class="footer-section">
              <h3>About</h3>
              <ul>
                <li><router-link to="/about">About Us</router-link></li>
                <li><router-link to="/blog">Blog</router-link></li>
                <li><a href="mailto:info@profilaunch.com">Contact</a></li>
                <li><router-link to="/faq">FAQ</router-link></li>
              </ul>
            </div>
            <div class="footer-section">
              <h3>Legal</h3>
              <ul>
                <li><router-link to="/terms">Terms of Service</router-link></li>
                <li><router-link to="/privacy">Privacy Policy</router-link></li>
                <li><router-link to="/code-of-conduct">Code of Conduct</router-link></li>
              </ul>
            </div>
          </div>
        </div>
        <div class="footer-bottom">
          <div class="footer-logo">
            <router-link to="/">
              <svg width="96" height="24" viewBox="0 0 96 24" xmlns="http://www.w3.org/2000/svg">
                <path d="M12 0H0V24H12C18.627 24 24 18.627 24 12C24 5.373 18.627 0 12 0ZM12 16C7.582 16 4 13.314 4 10C4 6.686 7.582 4 12 4C16.418 4 20 6.686 20 10C20 13.314 16.418 16 12 16Z" fill="#375bb1"/>
                <path d="M39.001 4.72H33.353V18.16H36.305V14.096H39.001C42.193 14.096 44.209 12.224 44.209 9.392C44.209 6.56 42.193 4.72 39.001 4.72ZM38.785 11.552H36.305V7.264H38.785C40.225 7.264 41.161 8.048 41.161 9.392C41.161 10.736 40.225 11.552 38.785 11.552Z" fill="#333333"/>
                <path d="M51.619 18.352C54.875 18.352 57.091 16.064 57.091 12.784C57.091 9.504 54.875 7.216 51.619 7.216C48.363 7.216 46.147 9.504 46.147 12.784C46.147 16.064 48.363 18.352 51.619 18.352ZM51.619 15.776C50.083 15.776 49.051 14.576 49.051 12.784C49.051 10.992 50.083 9.792 51.619 9.792C53.155 9.792 54.187 10.992 54.187 12.784C54.187 14.576 53.155 15.776 51.619 15.776Z" fill="#333333"/>
                <path d="M65.859 18.352C69.115 18.352 71.331 16.064 71.331 12.784C71.331 9.504 69.115 7.216 65.859 7.216C62.603 7.216 60.387 9.504 60.387 12.784C60.387 16.064 62.603 18.352 65.859 18.352ZM65.859 15.776C64.323 15.776 63.291 14.576 63.291 12.784C63.291 10.992 64.323 9.792 65.859 9.792C67.395 9.792 68.427 10.992 68.427 12.784C68.427 14.576 67.395 15.776 65.859 15.776Z" fill="#333333"/>
                <path d="M79.294 7.216C77.086 7.216 75.038 8.272 74.262 10.16L76.87 11.104C77.214 10.192 78.102 9.632 79.294 9.632C80.67 9.632 81.478 10.32 81.478 11.488H79.358C76.454 11.488 74.694 12.64 74.694 14.72C74.694 16.8 76.358 18.16 78.854 18.16C80.29 18.16 81.254 17.68 81.894 16.672V17.952H84.494V11.744C84.494 8.88 82.526 7.216 79.294 7.216ZM79.73 15.936C78.534 15.936 77.694 15.36 77.694 14.448C77.694 13.536 78.438 13.008 79.73 13.008H81.478V13.856C81.478 15.072 80.798 15.936 79.73 15.936Z" fill="#333333"/>
                <path d="M94.2699 7.408H91.2219V5.2H88.2699V7.408H86.2219V9.856H88.2699V14.864C88.2699 17.152 89.5899 18.256 93.0219 17.952V15.648C91.5339 15.728 91.2219 15.488 91.2219 14.864V9.856H94.2699V7.408Z" fill="#333333"/>
              </svg>
            </router-link>
          </div>
          <div class="copyright">
            © 2025 ProfiLaunch. All rights reserved.
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script>
import AiAssistPane from './components/AiAssistPane.vue';
import { auth } from './firebase';
import { signOut } from 'firebase/auth';
import { onAuthStateChanged } from 'firebase/auth';

export default {
  name: 'App',
  components: { AiAssistPane },
  data() {
    return {
      darkMode: false,
      isLoggedIn: false,
      userAvatar: 'https://www.gravatar.com/avatar/00000000000000000000000000000000?d=mp&f=y',
      showUserMenu: false,
      showMobileMenu: false,
      showAiPane: true
    };
  },
  methods: {
    toggleDarkMode() {
      this.darkMode = !this.darkMode;
      if (this.darkMode) {
        document.body.classList.add('dark-mode');
      } else {
        document.body.classList.remove('dark-mode');
      }
    },
    toggleUserMenu() {
      this.showUserMenu = !this.showUserMenu;
    },
    async handleLogout() {
      // Placeholder for logout logic
      await signOut(auth);
      this.isLoggedIn = false;
      this.showUserMenu = false;
      this.showMobileMenu = false;
      this.$router.push('/login');
    },
    toggleMobileMenu() {
      this.showMobileMenu = !this.showMobileMenu;
    },
    toggleAiPane() {
      this.showAiPane = !this.showAiPane;
    }
  },
  mounted() {
    onAuthStateChanged(auth, (user) => {
      this.isLoggedIn = !!user;
      this.userAvatar = user?.photoURL || 'https://www.gravatar.com/avatar/00000000000000000000000000000000?d=mp&f=y';
      // Optionally, fetch more user info from Firestore here
    });
  },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Nunito+Sans:wght@400;600;700&family=Space+Mono&display=swap');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Nunito Sans', sans-serif;
  color: #333;
  background-color: #f9f8f7;
  line-height: 1.5;
}

.dark-mode,
body.dark-mode {
  background-color: #181a1b !important;
  color: #f9f8f7 !important;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

/* Header Styles */
.header {
  background-color: #fff;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  position: sticky;
  top: 0;
  z-index: 100;
}

.dark-mode .header {
  background-color: #23272a;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 0;
}

.logo a {
  display: inline-block;
}

.main-nav {
  display: flex;
  gap: 30px;
}

.nav-link {
  color: #333;
  text-decoration: none;
  font-weight: 600;
  font-size: 16px;
  transition: color 0.2s;
}

.nav-link:hover, .nav-link.router-link-active {
  color: #375bb1;
}

.dark-mode .nav-link {
  color: #f9f8f7;
}

.nav-actions {
  display: flex;
  gap: 15px;
  align-items: center;
}

.theme-toggle {
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  margin-right: 10px;
}

.user-menu {
  position: relative;
  cursor: pointer;
  display: flex;
  align-items: center;
}

.user-avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: 2px solid #375bb1;
  object-fit: cover;
}

.user-menu-dropdown {
  position: absolute;
  top: 40px;
  right: 0;
  background: #fff;
  border: 1px solid #e5e5e5;
  border-radius: 8px;
  box-shadow: 0 4px 24px rgba(0,0,0,0.08);
  min-width: 180px;
  z-index: 10;
  padding: 8px 0;
}

.dark-mode .user-menu-dropdown {
  background: #23272a;
  border-color: #333;
}

.dropdown-item {
  display: block;
  padding: 10px 20px;
  color: #333;
  text-decoration: none;
  font-size: 15px;
  cursor: pointer;
  background: none;
  border: none;
  text-align: left;
  width: 100%;
}

.dropdown-item:hover {
  background: #f0f6ff;
  color: #375bb1;
}

.dark-mode .dropdown-item {
  color: #f9f8f7;
}

.dropdown-divider {
  height: 1px;
  background: #e5e5e5;
  margin: 8px 0;
}

.logout-btn {
  color: #e74c3c;
}

.login-btn, .signup-btn {
  background-color: #375bb1;
  color: #fff;
  border: none;
  padding: 8px 16px;
  border-radius: 4px;
  text-decoration: none;
  font-weight: 600;
  transition: all 0.2s;
  margin-left: 5px;
}

.login-btn {
  background: #fff;
  color: #375bb1;
  border: 1px solid #375bb1;
}

.login-btn:hover {
  background: #f0f6ff;
}

.signup-btn:hover {
  background: #2d4a8e;
}

.menu-toggle {
  display: none;
  background: none;
  border: none;
  cursor: pointer;
}

.menu-icon {
  display: block;
  width: 24px;
  height: 2px;
  background-color: #333;
  position: relative;
}

.menu-icon::before, .menu-icon::after {
  content: '';
  position: absolute;
  width: 24px;
  height: 2px;
  background-color: #333;
  left: 0;
}

.menu-icon::before {
  top: -8px;
}

.menu-icon::after {
  bottom: -8px;
}

.dark-mode .menu-icon,
.dark-mode .menu-icon::before,
.dark-mode .menu-icon::after {
  background-color: #f9f8f7;
}

/* Mobile Menu */
.mobile-menu {
  display: none;
}

.mobile-menu {
  display: flex;
  flex-direction: column;
  background: #fff;
  border-top: 1px solid #e5e5e5;
  padding: 20px 0;
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  z-index: 200;
}

.dark-mode .mobile-menu {
  background: #23272a;
  border-color: #333;
}

.mobile-nav-link {
  color: #333;
  text-decoration: none;
  font-weight: 600;
  font-size: 18px;
  padding: 12px 30px;
  transition: background 0.2s, color 0.2s;
}

.mobile-nav-link:hover {
  background: #f0f6ff;
  color: #375bb1;
}

.dark-mode .mobile-nav-link {
  color: #f9f8f7;
}

.mobile-divider {
  height: 1px;
  background: #e5e5e5;
  margin: 10px 0;
}

.dark-mode .mobile-divider {
  background: #333;
}

/* Footer Styles */
.footer {
  background-color: #f9f8f7;
  padding: 60px 0 30px;
  margin-top: 60px;
}

.dark-mode .footer {
  background-color: #23272a;
}

.footer-content {
  display: flex;
  justify-content: space-between;
  margin-bottom: 40px;
}

.footer-statement {
  max-width: 300px;
}

.footer-statement p {
  margin-bottom: 20px;
  font-weight: 600;
}

.social-links {
  display: flex;
  gap: 15px;
}

.social-icon {
  display: inline-block;
  width: 24px;
  height: 24px;
  background-size: contain;
  background-repeat: no-repeat;
}

.footer-links {
  display: flex;
  gap: 60px;
}

.footer-section h3 {
  font-size: 16px;
  margin-bottom: 20px;
}

.footer-section ul {
  list-style: none;
}

.footer-section li {
  margin-bottom: 10px;
}

.footer-section a {
  color: #666;
  text-decoration: none;
  font-size: 14px;
  transition: color 0.2s;
}

.footer-section a:hover {
  color: #375bb1;
}

.dark-mode .footer-section a {
  color: #f9f8f7;
}

.footer-bottom {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 30px;
  border-top: 1px solid #e5e5e5;
}

.dark-mode .footer-bottom {
  border-top: 1px solid #333;
}

.copyright {
  font-size: 14px;
  color: #666;
}

.dark-mode .copyright {
  color: #aaa;
}

/* Responsive */
@media (max-width: 768px) {
  .main-nav, .nav-actions {
    display: none;
  }

  .menu-toggle {
    display: block;
  }

  .mobile-menu {
    display: flex;
  }

  .footer-content {
    flex-direction: column;
    gap: 40px;
  }

  .footer-links {
    flex-direction: column;
    gap: 30px;
  }

  .footer-bottom {
    flex-direction: column;
    gap: 20px;
    text-align: center;
  }
}

.ai-fab {
  position: fixed;
  bottom: 32px;
  right: 32px;
  z-index: 1200;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: #375bb1;
  color: #fff;
  border: none;
  box-shadow: 0 4px 16px rgba(55,91,177,0.15);
  font-size: 2rem;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background 0.2s;
}
.ai-fab:hover {
  background: #2d4a8e;
}
@media (max-width: 900px) {
  .ai-fab { display: none; }
}
</style>
