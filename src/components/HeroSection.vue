<template>
  <div class="hero-section">
    <div class="hero-content">
      <h1>Redefining economic opportunities for builders with <span>hackathons</span></h1>
      <div class="animated-shapes">
        <div class="shape shape-1"/>
        <div class="shape shape-2"/>
        <div class="shape shape-3"/>
        <div class="shape shape-4"/>
        <div class="shape shape-5"/>
        <div class="shape shape-6"/>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HeroSection'
};
</script>

<style scoped>
.hero-section {
  background-color: #f9f8f7;
  padding: 100px 20px;
  text-align: center;
  position: relative;
  overflow: hidden;
}

.hero-content {
  max-width: 1200px;
  margin: 0 auto;
  position: relative;
  z-index: 2;
}

.hero-section h1 {
  font-size: 48px;
  font-weight: 700;
  margin-bottom: 40px;
  max-width: 900px;
  margin-left: auto;
  margin-right: auto;
  position: relative;
  color: #333;
}

.hero-section h1 span {
  display: block;
  color: #46d6ae;
}

.animated-shapes {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1;
  pointer-events: none;
}

.shape {
  position: absolute;
  border-radius: 50%;
  opacity: 0.7;
  animation: float 15s infinite ease-in-out;
}

.shape-1 {
  width: 80px;
  height: 80px;
  background-color: #f4cd4d;
  top: 10%;
  left: 10%;
  animation-delay: 0s;
}

.shape-2 {
  width: 60px;
  height: 60px;
  background-color: #95add5;
  top: 20%;
  right: 15%;
  animation-delay: 2s;
}

.shape-3 {
  width: 100px;
  height: 100px;
  background-color: #46d6ae;
  bottom: 15%;
  left: 20%;
  animation-delay: 4s;
}

.shape-4 {
  width: 50px;
  height: 50px;
  background-color: #e3b185;
  bottom: 25%;
  right: 25%;
  animation-delay: 6s;
}

.shape-5 {
  width: 70px;
  height: 70px;
  background-color: #c0cff4;
  top: 40%;
  left: 30%;
  animation-delay: 8s;
}

.shape-6 {
  width: 90px;
  height: 90px;
  background-color: #e3ce9c;
  bottom: 30%;
  right: 10%;
  animation-delay: 10s;
}

@keyframes float {
  0% {
    transform: translateY(0) translateX(0) rotate(0);
  }
  25% {
    transform: translateY(-15px) translateX(10px) rotate(5deg);
  }
  50% {
    transform: translateY(10px) translateX(-10px) rotate(-5deg);
  }
  75% {
    transform: translateY(-5px) translateX(15px) rotate(3deg);
  }
  100% {
    transform: translateY(0) translateX(0) rotate(0);
  }
}

@media (max-width: 768px) {
  .hero-section h1 {
    font-size: 36px;
  }

  .shape {
    transform: scale(0.7);
  }
}
</style>
