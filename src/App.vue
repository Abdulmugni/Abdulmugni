<template>
  <LivingInteraction />
  <div class="site">
    <Navbar />
    <main>
      <Hero />
      <About />
      <Skills />
      <Experience />
      <Projects />
      <InteractiveExperience />
      <Contact />
    </main>
    <Footer />
  </div>
</template>

<script setup>
import LivingInteraction from "./components/LivingInteraction.vue"
import Navbar from "./components/Navbar.vue"
import Hero from "./components/Hero.vue"
import About from "./components/About.vue"
import Skills from "./components/Skills.vue"
import Experience from "./components/Experience.vue"
import Projects from "./components/Projects.vue"
import Contact from "./components/Contact.vue"
import Footer from "./components/Footer.vue"
import InteractiveExperience from './components/InteractiveExperience.vue'
</script>
