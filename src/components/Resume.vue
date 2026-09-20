<template>
  <section id="resume" class="section resume-section">
    <div class="container">
      <div class="section-head">
        <span class="section-number">04</span>
        <span class="section-label">RESUME / PROFILE</span>
      </div>

      <!-- Simple tab navigation -->
      <div class="resume-tabs">
        <div class="tabs">
          <button
            v-for="(tab, idx) in tabs"
            :key="tab.id"
            @click="active = idx"
            :class="{ active: active === idx }"
            class="tab-button"
          >
            {{ tab.label }}
          </button>
        </div>
        <div class="tab-content">
          <!-- Experience Tab -->
          <div v-if="active === 0" class="tab-panel">
            <h3>Experience</h3>
            <p>
              Senior Power BI and Data Engineer &amp; AI
              enthusiast with 4+ years of building end‑to‑end analytical systems.
              At Accenture I lead enterprise reporting, performance tuning,
              and data model design for global clients, delivering 30+ strategic
              dashboards and high‑impact analytics.
            </p>
          </div>
          <!-- Education Tab -->
          <div v-else-if="active === 1" class="tab-panel">
            <h3>Education</h3>
            <p>
              B.Sc. in Computer Science, University of Hyderabad (2020).
              Focus on data management, machine learning and user‑centered UI.
            </p>
          </div>
          <!-- Skills Tab -->
          <div v-else-if="active === 2" class="tab-panel">
            <h3>Skills</h3>
            <p>
              Business Intelligence (Power BI, DAX, Power Query),
              Data Engineering (SQL, Azure Data Factory, Synapse),
              Automation &amp; AI (Python, Azure Functions, Agentic workflows),
              Cloud &amp; DevOps (Git, GitHub, CI/CD, Azure).
            </p>
          </div>
          <!-- Certifications Tab -->
          <div v-else-if="active === 3" class="tab-panel">
            <h3>Certifications</h3>
            <p>
              Microsoft Certified: Data Analyst Associate,
              Microsoft Certified: Azure AI Engineer Associate,
              Certified Scrum Master (CSM).
            </p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref } from 'vue'

const tabs = [
  { id: 'experience', label: 'Experience' },
  { id: 'education', label: 'Education' },
  { id: 'skills', label: 'Skills' },
  { id: 'certifications', label: 'Certifications' },
]

// Index of the active tab
const active = ref(0)
</script>

<style scoped>
.tabs {
  display: flex;
  gap: 12px;
  margin-bottom: 24px;
}

.tab-button {
  background: none;
  border: 1px solid var(--line);
  border-radius: 8px;
  padding: 6px 12px;
  color: var(--muted);
  font: 9px "DM Mono";
  cursor: pointer;
  transition: .25s;
}

.tab-button.active,
.tab-button:hover {
  background: rgba(88,166,255,.07);
  color: var(--text);
  border-color: var(--accent);
}

.tab-content {
  background: rgba(12,17,25,.75);
  border: 1px solid var(--line);
  border-radius: 12px;
  padding: 24px;
}

.tab-panel h3 {
  margin-bottom: 12px;
  font-size: 24px;
  color: var(--accent);
}

.tab-panel p {
  color: var(--muted);
  line-height: 1.5;
}
</style>
