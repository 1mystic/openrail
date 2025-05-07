<template>
  <div class="hackathons-page">
    <div class="page-header">
      <div class="filter-tabs">
        <button
          class="filter-tab"
          :class="{ active: currentTab === 'discover' }"
          @click="setTab('discover')"
        >
          DISCOVER
        </button>
        <button
          class="filter-tab"
          :class="{ active: currentTab === 'hackathons' }"
          @click="setTab('hackathons')"
        >
          HACKATHONS
        </button>
        <button
          class="filter-tab"
          :class="{ active: currentTab === 'builders' }"
          @click="setTab('builders')"
        >
          BUILDERS
        </button>
      </div>
      <div class="search-bar">
        <input
          type="text"
          placeholder="Type to begin search or use this global hotkeys"
          v-model="searchQuery"
        >
        <span class="keyboard-hint">Ctrl</span>
        <span class="keyboard-hint">K</span>
      </div>
      <div class="my-hackathons">
        <a href="/applied" class="my-hackathons-btn">Your hackathons</a>
      </div>
    </div>

    <div class="featured-section">
      <h2>Featured</h2>
      <div class="featured-card" v-for="(hackathon, index) in featuredHackathons" :key="'featured-'+index">
        <div class="featured-card-content">
          <h3>{{ hackathon.title }}</h3>
          <div class="hackathon-type">Hackathon</div>
          <div class="hackathon-tags">
            <span class="tag" v-for="(tag, tagIndex) in hackathon.tags" :key="tagIndex">{{ tag }}</span>
          </div>
          <div class="participants" v-if="hackathon.participants">
            <div class="participant-count">{{ hackathon.participants }} participating</div>
          </div>
          <div class="hackathon-details">
            <div class="location-type">{{ hackathon.locationType }}</div>
            <div class="status">{{ hackathon.status }}</div>
            <div class="live-indicator" v-if="hackathon.isLive">Live</div>
          </div>
          <a :href="hackathon.link" class="apply-now">Apply now</a>
        </div>
      </div>
    </div>

    <div class="open-hackathons-section">
      <div class="section-header">
        <h2>Open</h2>
        <a href="/hackathons/open" class="view-all">All open hackathons</a>
      </div>
      <div class="hackathons-grid">
        <div class="hackathon-card" v-for="(hackathon, index) in openHackathons" :key="'open-'+index">
          <div class="hackathon-card-header">
            <h3>{{ hackathon.title }}</h3>
            <div class="hackathon-type">Hackathon</div>
          </div>
          <div class="hackathon-tags">
            <span class="tag" v-for="(tag, tagIndex) in hackathon.tags" :key="tagIndex">{{ tag }}</span>
          </div>
          <div class="participants" v-if="hackathon.participants">
            <div class="participant-images">
              <!-- Here we would show participant images -->
            </div>
            <div class="participant-count">{{ hackathon.participants }} participating</div>
          </div>
          <div class="hackathon-details">
            <div class="location-type">{{ hackathon.locationType }}</div>
            <div class="status">{{ hackathon.status }}</div>
            <div class="date" v-if="hackathon.date">{{ hackathon.date }}</div>
          </div>
          <a :href="hackathon.link" class="apply-now">Apply now</a>
        </div>
      </div>
    </div>

    <div class="past-hackathons-section">
      <div class="section-header">
        <h2>Past</h2>
        <a href="/hackathons/past" class="view-all">All past hackathons</a>
      </div>
      <div class="hackathons-grid">
        <div class="hackathon-card" v-for="(hackathon, index) in pastHackathons" :key="'past-'+index">
          <div class="hackathon-card-header">
            <h3>{{ hackathon.title }}</h3>
            <div class="hackathon-type">Hackathon</div>
          </div>
          <div class="hackathon-tags">
            <span class="tag" v-for="(tag, tagIndex) in hackathon.tags" :key="tagIndex">{{ tag }}</span>
          </div>
          <div class="participants" v-if="hackathon.participants">
            <div class="participant-images">
              <!-- Here we would show participant images -->
            </div>
            <div class="participant-count">{{ hackathon.participants }} participated</div>
          </div>
          <div class="hackathon-details">
            <div class="location-type">{{ hackathon.locationType }}</div>
            <div class="status">Ended</div>
          </div>
          <a :href="hackathon.link" class="view-projects">See projects</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Hackathons',
  data() {
    return {
      currentTab: 'hackathons',
      searchQuery: '',
      featuredHackathons: [
        {
          title: 'Sui Overflow 2025',
          tags: ['Blockchain'],
          locationType: 'Online',
          status: 'Open',
          isLive: true,
          link: '#'
        },
        {
          title: 'Bio x AI Hackathon 2025',
          tags: ['Blockchain', 'AI/ML', 'HealthTech'],
          participants: '+1000',
          locationType: 'Online',
          status: 'Open',
          isLive: true,
          link: '#'
        }
      ],
      openHackathons: [
        {
          title: 'InnovXChange',
          tags: ['No Restrictions'],
          participants: '+250',
          locationType: 'Online',
          status: 'Open',
          date: 'Starts 29/05/25',
          link: '#'
        },
        {
          title: 'Hack The Grid - LUKSO',
          tags: ['Blockchain', 'AI/ML'],
          participants: '+1000',
          locationType: 'Online',
          status: 'Open',
          isLive: true,
          link: '#'
        },
        {
          title: 'DevShastra',
          tags: ['No Restrictions'],
          participants: '+1000',
          locationType: 'Offline',
          status: 'Open',
          date: 'Starts 15/05/25',
          link: '#'
        },
        {
          title: 'HackNeeti 1.0',
          tags: ['No Restrictions'],
          participants: '+500',
          locationType: 'Offline',
          status: 'Open',
          date: 'Starts 19/05/25',
          link: '#'
        }
      ],
      pastHackathons: [
        {
          title: 'Sprinthacks 3.0',
          tags: ['No Restrictions'],
          participants: '+1000',
          locationType: 'Offline',
          link: '#'
        },
        {
          title: 'HACKHAZARDS \'25',
          tags: ['No Restrictions'],
          participants: '+15000',
          locationType: 'Online',
          link: '#'
        },
        {
          title: 'HackO\'Clock 2025',
          tags: ['No Restrictions'],
          participants: '+1000',
          locationType: 'Offline',
          link: '#'
        }
      ]
    };
  },
  methods: {
    setTab(tab) {
      this.currentTab = tab;
    }
  }
};
</script>

<style scoped>
.hackathons-page {
  font-family: 'Nunito Sans', sans-serif;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 40px;
  flex-wrap: wrap;
}

.filter-tabs {
  display: flex;
  gap: 10px;
}

.filter-tab {
  padding: 8px 16px;
  border: none;
  background: none;
  cursor: pointer;
  font-weight: 600;
  color: #333;
  position: relative;
}

.filter-tab.active {
  color: #375bb1;
}

.filter-tab.active:after {
  content: '';
  position: absolute;
  bottom: -5px;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: #375bb1;
}

.search-bar {
  display: flex;
  align-items: center;
  background-color: #f5f5f5;
  border-radius: 20px;
  padding: 8px 15px;
}

.search-bar input {
  border: none;
  background: none;
  outline: none;
  width: 300px;
  margin-right: 10px;
}

.keyboard-hint {
  display: inline-block;
  background-color: #e5e5e5;
  border-radius: 3px;
  padding: 2px 5px;
  margin-left: 5px;
  font-size: 12px;
  font-weight: 600;
}

.my-hackathons-btn {
  display: inline-block;
  background-color: #f0f6ff;
  color: #375bb1;
  padding: 8px 16px;
  border-radius: 20px;
  text-decoration: none;
  font-weight: 600;
}

.featured-section, .open-hackathons-section, .past-hackathons-section {
  margin-bottom: 60px;
}

.featured-section h2, .section-header h2 {
  font-size: 24px;
  margin-bottom: 20px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.view-all {
  color: #375bb1;
  text-decoration: none;
  font-size: 14px;
  font-weight: 600;
  display: flex;
  align-items: center;
}

.featured-card {
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin-bottom: 30px;
  overflow: hidden;
}

.featured-card-content {
  padding: 20px;
}

.hackathons-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
}

.hackathon-card {
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  padding: 20px;
}

.hackathon-card-header {
  margin-bottom: 10px;
}

.hackathon-card h3, .featured-card h3 {
  font-size: 20px;
  margin-bottom: 5px;
  color: #333;
}

.hackathon-type {
  font-size: 14px;
  color: #666;
  margin-bottom: 10px;
}

.hackathon-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
  margin-bottom: 15px;
}

.tag {
  background-color: #f0f0f0;
  padding: 4px 10px;
  border-radius: 15px;
  font-size: 12px;
  color: #333;
}

.participants {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}

.participant-images {
  margin-right: 10px;
}

.participant-count {
  font-size: 14px;
  color: #666;
}

.hackathon-details {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}

.location-type, .status, .live-indicator, .date {
  font-size: 12px;
  padding: 4px 10px;
  border-radius: 15px;
  background-color: #f0f0f0;
  color: #333;
}

.live-indicator {
  background-color: #f4cd4d;
  color: #333;
}

.apply-now, .view-projects {
  display: inline-block;
  background-color: #375bb1;
  color: white;
  padding: 8px 16px;
  border-radius: 4px;
  text-decoration: none;
  font-weight: 600;
}

/* Responsive */
@media (max-width: 768px) {
  .page-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 20px;
  }

  .search-bar {
    width: 100%;
  }

  .search-bar input {
    width: 100%;
  }

  .hackathons-grid {
    grid-template-columns: 1fr;
  }
}
</style>
