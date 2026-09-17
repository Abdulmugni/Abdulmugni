<template>
  <section id="top" class="hero">
    <div class="hero-noise"></div>
    <div class="hero-grid"></div>

    <div class="hero-glow glow-a"></div>
    <div class="hero-glow glow-b"></div>

    <div class="container hero-inner">
      <div class="hero-copy reveal">
        <div class="eyebrow">
          <span class="pulse"></span>
          DATA • ANALYTICS • AI
        </div>

        <div class="hero-kicker">POWER BI DEVELOPER / DATA ENGINEER</div>

        <h1>
          I build
          <span class="hero-outline">data systems</span>
          <br />
          people can <span class="gradient-text">trust.</span>
        </h1>

        <p class="hero-lead">
          Transforming complex business data into intelligent dashboards,
          scalable pipelines and AI-powered experiences.
        </p>

        <div class="hero-actions">
          <a class="btn btn-primary" href="#projects">
            View selected work <span>↗</span>
          </a>
          <a class="btn btn-glass" href="#about">
            Discover more <span>↓</span>
          </a>
        </div>

        <div class="hero-stats">
          <div class="hero-stat">
            <strong>4.7<span>+</span></strong>
            <small>YEARS EXPERIENCE</small>
          </div>
          <div class="stat-line"></div>
          <div class="hero-stat">
            <strong>30<span>+</span></strong>
            <small>REPORTS REBUILT</small>
          </div>
          <div class="stat-line"></div>
          <div class="hero-stat">
            <strong>10<span>+</span></strong>
            <small>COMPLEX BUILDS</small>
          </div>
        </div>
      </div>

      <div class="hero-stage reveal delay-2">
        <div class="stage-orbit orbit-one"></div>
        <div class="stage-orbit orbit-two"></div>
        <div class="stage-orbit orbit-three"></div>

        <div class="stage-core">
          <div class="core-top">
            <span>SYSTEM STATUS</span>
            <span class="system-live"><i></i> ONLINE</span>
          </div>

          <div class="core-title">ANALYTICS<br /><em>ENGINE</em></div>

          <div class="signal">
            <span></span><span></span><span></span><span></span><span></span>
            <span></span><span></span><span></span><span></span><span></span>
          </div>

          <div class="core-footer">
            <span>DATA FLOW</span>
            <strong>98.7%</strong>
          </div>
        </div>

        <div class="tech-float float-powerbi">
          <b>▦</b>
          <span>Power BI</span>
        </div>

        <div class="tech-float float-ai">
          <b>✦</b>
          <span>AI AGENTS</span>
        </div>

        <div class="tech-float float-fabric">
          <b>◈</b>
          <span>FABRIC</span>
        </div>

        <div class="coordinate">18.5204° N<br />73.8567° E</div>
      </div>
    </div>

    <div class="hero-bottom">
      <div class="container hero-bottom-inner">
        <span>SCROLL TO EXPLORE</span>
        <div class="scroll-line"><i></i></div>
        <span>BASED IN INDIA</span>
      </div>
    </div>
  </section>
</template>

<script setup>
import { onMounted } from "vue"

onMounted(() => {
  const reveal = () => {
    document.querySelectorAll(".reveal").forEach(el => {
      const r = el.getBoundingClientRect()
      if (r.top < window.innerHeight * .88) el.classList.add("visible")
    })
  }

  window.addEventListener("scroll", reveal, { passive: true })
  reveal()
})
</script>
