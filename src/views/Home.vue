<template>
  <div class="home">
    <HeroSection />

    <div class="stats-section">
      <h2>We drop jaws!</h2>
      <p>In January 2019, we powered our first community hackathon. Since then we've grown to</p>
      <div class="stats-container">
        <div class="stat-box">
          <h3>800,000+</h3>
          <p>builders</p>
        </div>
        <div class="stat-box">
          <h3>75,000+</h3>
          <p>projects</p>
        </div>
        <div class="stat-box">
          <h3>1,300+</h3>
          <p>hackathons</p>
        </div>
      </div>
    </div>

    <div class="happening-now-section">
      <h2>Happening now</h2>
      <div class="hackathon-grid">
        <div class="hackathon-card" v-for="(hackathon, index) in hackathons" :key="index">
          <h3>{{ hackathon.title }}</h3>
          <p>{{ hackathon.date }}</p>
          <p>{{ hackathon.location }}</p>
          <a :href="hackathon.link" class="participate-btn">Participate</a>
        </div>
      </div>
      <a href="/hackathons" class="see-all-btn">See all</a>
    </div>

    <div class="testimonials-section">
      <div class="testimonial" v-for="(testimonial, index) in testimonials" :key="index">
        <q>{{ testimonial.quote }}</q>
        <p class="author">{{ testimonial.author }}</p>
        <p class="role">{{ testimonial.role }}</p>
      </div>
    </div>

    <div class="features-section">
      <div class="feature">
        <h2>Your digital up-to-date resumé</h2>
        <p>Showcase to the world your skills, links to your social profile, where you have worked and what you have worked on and the projects you have built.</p>
      </div>

      <div class="feature">
        <h2>A showcase of all your projects</h2>
        <p>Give your weekend projects, side projects, hobby projects, serious ventures a place to breathe, invite collaborators and inspire other builders.</p>
      </div>

      <div class="feature">
        <h2>Your portal to the best hackathons</h2>
        <p>Applying to hackathons on Devfolio is as simple as a click of a button. We save all the required info so that you dont have to fill them over and over again.</p>
      </div>

      <div class="feature">
        <h2>Your skill assessment playground</h2>
        <p>Take our quiz, earn a badge and see where you stand among the rest of the builders.</p>
      </div>
    </div>

    <div class="community-section">
      <h2>We speak, we listen, we discuss, we grow.</h2>
      <p>Share ideas, feedback, connect with people over the love of our community.</p>
      <a href="https://t.me/devfolio" class="community-btn">Join our community</a>
    </div>
  </div>
</template>

<script>
import HeroSection from '../components/HeroSection.vue';

export default {
  name: 'Home',
  components: {
    HeroSection
  },
  data() {
    return {
      hackathons: [
        {
          title: 'Hack The Grid - LUKSO',
          date: 'March 25, 2025',
          location: 'Happening Online',
          link: '#'
        },
        {
          title: 'HackNeeti 1.0',
          date: 'May 19, 2025',
          location: 'Noida, India',
          link: '#'
        },
        {
          title: 'DevShastra',
          date: 'May 15, 2025',
          location: 'Ghaziabad, India',
          link: '#'
        }
      ],
      testimonials: [
        {
          quote: 'The fact that our hack [InstaDApp] saw the light of the day is a great signal about the success of the Devfolio hackathons.',
          author: 'Samyak and Sowmay Jain',
          role: 'Co-founders, InstaDApp'
        },
        {
          quote: 'Magical hacking experience plus fun and enriched networking. The journey to build Incento started at one of such amazing hackathon organized by Devfolio.',
          author: 'Chetan Badhe',
          role: 'Co-founder, Incento'
        },
        {
          quote: 'InOut connected me to awesome developers & made me feel inclusive. Devfolio has been doing an amazing job in keeping the community intact.',
          author: 'Devika K Das',
          role: 'Software Engineer, Google'
        }
      ]
    };
  }
};
</script>

<style scoped>
.home {
  font-family: 'Nunito Sans', sans-serif;
  color: #333;
}

.stats-section {
  padding: 60px 20px;
  text-align: center;
  max-width: 1200px;
  margin: 0 auto;
}

.stats-section h2 {
  font-size: 36px;
  margin-bottom: 20px;
}

.stats-container {
  display: flex;
  justify-content: space-around;
  margin-top: 40px;
}

.stat-box {
  padding: 20px;
}

.stat-box h3 {
  font-size: 42px;
  color: #375bb1;
  margin-bottom: 10px;
}

.happening-now-section {
  padding: 60px 20px;
  max-width: 1200px;
  margin: 0 auto;
}

.happening-now-section h2 {
  font-size: 36px;
  margin-bottom: 40px;
  text-align: center;
}

.hackathon-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 30px;
  margin-bottom: 40px;
}

.hackathon-card {
  background-color: #fff;
  border-radius: 8px;
  padding: 30px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.hackathon-card h3 {
  font-size: 24px;
  margin-bottom: 10px;
  color: #375bb1;
}

.participate-btn {
  display: inline-block;
  background-color: #375bb1;
  color: white;
  padding: 10px 20px;
  border-radius: 4px;
  text-decoration: none;
  font-weight: 600;
  margin-top: 20px;
}

.see-all-btn {
  display: block;
  text-align: center;
  color: #375bb1;
  text-decoration: none;
  font-weight: 600;
  margin-top: 20px;
}

.testimonials-section {
  background-color: #f9f8f7;
  padding: 60px 20px;
  text-align: center;
}

.testimonial {
  max-width: 800px;
  margin: 0 auto 40px;
}

.testimonial q {
  font-size: 24px;
  font-style: italic;
  display: block;
  margin-bottom: 20px;
}

.author {
  font-weight: 700;
  margin-bottom: 5px;
}

.role {
  color: #666;
}

.features-section {
  padding: 60px 20px;
  max-width: 1200px;
  margin: 0 auto;
}

.feature {
  margin-bottom: 60px;
}

.feature h2 {
  font-size: 32px;
  margin-bottom: 20px;
  color: #375bb1;
}

.feature p {
  font-size: 18px;
  max-width: 800px;
}

.community-section {
  background-color: #f4cd4d;
  padding: 60px 20px;
  text-align: center;
}

.community-section h2 {
  font-size: 36px;
  margin-bottom: 20px;
}

.community-btn {
  display: inline-block;
  background-color: #375bb1;
  color: white;
  padding: 12px 24px;
  border-radius: 4px;
  text-decoration: none;
  font-weight: 600;
  margin-top: 30px;
}

/* Responsive */
@media (max-width: 768px) {
  .hackathon-grid {
    grid-template-columns: 1fr;
  }

  .stats-container {
    flex-direction: column;
  }
}
</style>
