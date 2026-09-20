<template>
  <section id="resume" class="section resume-section">
    <div class="container">
      <div class="section-head">
        <span class="section-number">04</span>
        <span class="section-label">RESUME / BACKGROUND</span>
      </div>

      <div class="resume-tabs">
        <div class="tabs-nav">
          <button
            v-for="tab in tabs"
            :key="tab.id"
            :class="['tab-button', activeTab === tab.id && 'active']"
            @click="activeTab = tab.id"
          >
            {{ tab.label }}
          </button>
        </div>
        <div class="tabs-content">
          <div v-if="activeTab === 'experience'" class="tab-panel">
            <h3>Professional Experience</h3>
            <ul class="resume-list">
              <li>
                <strong>Power BI Developer – Accenture</strong> (2023 – Present)
                <p>Driving enterprise‑wide analytics, building scalable dashboards, and automating data pipelines to enable data‑driven decision making.</p>
              </li>
              <li>
                <strong>Test Engineer / Power BI – GlobalStep</strong> (2022 – 2023)
                <p>Combined quality engineering with analytics, establishing testing frameworks and data models that improved release velocity.</p>
              </li>
            </ul>
          </div>

          <div v-else-if="activeTab === 'education'" class="tab-panel">
            <h3>Education</h3>
            <ul class="resume-list">
              <li>
                <strong>B.Sc. Computer Science</strong>, University of XYZ (2021)
                <p>Focused on data engineering, visual analytics, and AI‑driven systems.</p>
              </li>
            </ul>
          </div>

          <div v-else-if="activeTab === 'skills'" class="tab-panel">
            <h3>Core Skills</h3>
            <div class="skill-cloud">
              <span>Power BI</span>
              <span>DAX</span>
              <span>SQL</span>
              <span>Azure Data Factory</span>
              <span>Python</span>
              <span>AI Agents</span>
              <span>Git</span>
              <span>CI/CD</span>
            </div>
          </div>

          <div v-else-if="activeTab === 'certifications'" class="tab-panel">
            <h3>Certifications</h3>
            <ul class="resume-list">
              <li>Microsoft Certified: Data Analyst Associate (Power BI)</li>
              <li>Microsoft Certified: Azure Fundamentals</li>
              <li>ISTQB Certified Tester</li>
            </ul>
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

const activeTab = ref('experience')
</script>

<style scoped>
.resume-tabs {
  margin-top: 30px;
}

.tabs-nav {
  display: flex;
  gap: 12px;
  margin-bottom: 20px;
}

.tab-button {
  background: transparent;
  border: 1px solid var(--line2);
  color: var(--muted);
  font-size: 11px;
  padding: 8px 12px;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.25s, color 0.25s, border-color 0.25s;
}

.tab-button:hover {
  background: var(--accent);
  color: var(--bg);
  border-color: var(--accent);
}

.tab-button.active {
  background: var(--accent);
  color: var(--bg);
  border-color: var(--accent);
}

.tab-panel h3 {
  margin-bottom: 12px;
  font-size: 24px;
  color: var(--text);
}

.resume-list {
  list-style: none;
  padding-left: 0;
  margin: 0;
  gap: 16px;
  display: flex;
  flex-direction: column;
}

.resume-list li {
  color: var(--muted);
}

.resume-list strong {
  display: block;
  color: var(--accent);
  margin-bottom: 4px;
}

.resume-list p {
  margin-top: 4px;
  color: var(--muted);
}
</style>
