<template>
  <header class="nav">
    <div class="nav-progress"></div>
    <div class="container nav-inner">
      <a href="#top" class="brand">
        <span class="brand-mark">
          <span>A</span><span>S</span>
        </span>
        <span class="brand-text">ABDULMUGNI<span>.</span></span>
      </a>

      <nav class="nav-links">
        <a href="#about"><span>01</span> About</a>
        <a href="#skills"><span>02</span> Skills</a>
        <a href="#experience"><span>03</span> Experience</a>
        <a href="#projects"><span>04</span> Work</a>
        <a href="#contact"><span>05</span> Contact</a>
      </nav>

      <a href="#contact" class="nav-cta">
        Let's connect <b>↗</b>
      </a>
    </div>
  </header>
</template>

<script setup>
import { onMounted } from "vue"

onMounted(() => {
  const update = () => {
    const h = document.documentElement
    const max = h.scrollHeight - h.clientHeight
    const progress = max > 0 ? (h.scrollTop / max) * 100 : 0
    document.documentElement.style.setProperty("--scroll-progress", progress + "%")
  }

  window.addEventListener("scroll", update, { passive: true })
  update()
})
</script>
