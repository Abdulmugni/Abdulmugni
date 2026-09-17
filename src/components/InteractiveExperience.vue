<template>
  <section id="experience" class="living-experience">
    <div class="living-grid"></div>

    <div class="section-shell">
      <div class="living-heading reveal">
        <div>
          <span class="eyebrow"><span class="pulse-dot"></span> SYSTEM / ONLINE</span>
          <h2>Don't just <span>read</span> the portfolio.<br />Interact with it.</h2>
        </div>

        <div class="system-status">
          <span class="status-line">
            <span class="status-light"></span>
            {{ systemState }}
          </span>
          <span class="status-time">{{ currentTime }}</span>
        </div>
      </div>

      <!-- LIVE TELEMETRY -->
      <div class="telemetry-grid reveal">
        <article class="telemetry-card telemetry-primary">
          <div class="telemetry-top">
            <span>LIVE TELEMETRY</span>
            <span class="telemetry-id">JARVIS / 01</span>
          </div>

          <div class="telemetry-main">
            <div class="signal-ring">
              <div class="signal-core"></div>
              <span>{{ signal }}%</span>
            </div>

            <div class="telemetry-copy">
              <strong>Engineering activity</strong>
              <p>{{ telemetryMessage }}</p>

              <div class="signal-bars">
                <i
                  v-for="(bar, index) in signalBars"
                  :key="index"
                  :style="{ height: bar + '%' }"
                ></i>
              </div>
            </div>
          </div>
        </article>

        <article class="telemetry-card">
          <span class="metric-label">YEARS BUILDING</span>
          <strong class="metric-number">{{ yearsExperience }}+</strong>
          <span class="metric-foot">BI · DATA · AUTOMATION · AI</span>
        </article>

        <article class="telemetry-card">
          <span class="metric-label">REPORTS REBUILT</span>
          <strong class="metric-number">{{ animatedReports }}+</strong>
          <span class="metric-foot">PERFORMANCE / UX / DELIVERY</span>
        </article>

        <article class="telemetry-card">
          <span class="metric-label">COMPLEX BUILDS</span>
          <strong class="metric-number">{{ animatedBuilds }}+</strong>
          <span class="metric-foot">POWER BI / DATA / AUTOMATION</span>
        </article>
      </div>

      <!-- COMMAND PALETTE -->
      <div class="command-card reveal">
        <div class="command-title">
          <span class="terminal-dot"></span>
          <span>portfolio://command-center</span>
        </div>

        <div class="command-input">
          <span class="command-prefix">›</span>
          <input
            v-model="command"
            aria-label="Portfolio command"
            placeholder="Try: projects, jarvis, skills, experience..."
            @keydown.enter="executeCommand"
          />
          <kbd>ENTER</kbd>
        </div>

        <div class="command-suggestions">
          <button
            v-for="item in commands"
            :key="item.command"
            type="button"
            @click="runCommand(item.command)"
          >
            {{ item.label }}
          </button>
        </div>

        <div v-if="commandResult" class="command-result" aria-live="polite">
          <span>✓</span> {{ commandResult }}
        </div>
      </div>

      <!-- DATA FLOW -->
      <div id="architecture" class="flow-section reveal">
        <div class="flow-heading">
          <span class="eyebrow">01 / DATA FLOW</span>
          <h3>From raw signal to useful intelligence.</h3>
          <p>
            The same thinking behind the work: connect systems,
            transform information, validate it, and turn it into decisions.
          </p>
        </div>

        <div class="living-flow">
          <div
            v-for="(node, index) in flowNodes"
            :key="node.name"
            class="flow-node-new"
            :class="{ active: activeNode === index }"
            @click="activeNode = index"
          >
            <span class="flow-number">0{{ index + 1 }}</span>
            <span class="flow-icon">{{ node.icon }}</span>
            <strong>{{ node.name }}</strong>
            <small>{{ node.detail }}</small>
            <span class="node-pulse"></span>
          </div>
        </div>

        <div class="flow-inspector">
          <span>ACTIVE NODE</span>
          <strong>{{ flowNodes[activeNode].name }}</strong>
          <p>{{ flowNodes[activeNode].description }}</p>
        </div>
      </div>

      <!-- PROJECT EXPLORER -->
      <div id="projects" class="project-explorer reveal">
        <div class="explorer-heading">
          <div>
            <span class="eyebrow">02 / PROJECT EXPLORER</span>
            <h3>Pick a system. Open it.</h3>
          </div>

          <div class="filter-row">
            <button
              v-for="filter in projectFilters"
              :key="filter"
              type="button"
              :class="{ selected: activeFilter === filter }"
              @click="activeFilter = filter"
            >
              {{ filter }}
            </button>
          </div>
        </div>

        <div class="project-grid-new">
          <article
            v-for="project in filteredProjects"
            :key="project.name"
            class="project-card-new"
            @click="selectedProject = project"
          >
            <div class="project-card-top">
              <span class="project-type">{{ project.type }}</span>
              <span class="project-arrow">↗</span>
            </div>

            <div class="project-visual" :class="project.visual">
              <div class="visual-grid"></div>
              <div class="visual-orbit"></div>
              <div class="visual-core">{{ project.symbol }}</div>
            </div>

            <div class="project-card-body">
              <h4>{{ project.name }}</h4>
              <p>{{ project.description }}</p>

              <div class="tech-row">
                <span v-for="tech in project.tech" :key="tech">{{ tech }}</span>
              </div>
            </div>
          </article>
        </div>
      </div>

      <!-- ENGINEERING TIMELINE -->
      <div id="experience-timeline" class="timeline-section reveal">
        <div class="flow-heading">
          <span class="eyebrow">03 / ENGINEERING JOURNEY</span>
          <h3>A career built around systems.</h3>
        </div>

        <div class="timeline">
          <article
            v-for="(item, index) in timeline"
            :key="item.company"
            class="timeline-item"
            :class="{ current: index === 1 }"
          >
            <div class="timeline-marker">
              <span>{{ index === 1 ? '●' : '○' }}</span>
            </div>

            <div class="timeline-content">
              <div class="timeline-meta">
                <span>{{ item.period }}</span>
                <span>{{ item.role }}</span>
              </div>
              <h4>{{ item.company }}</h4>
              <p>{{ item.description }}</p>
              <div class="tech-row">
                <span v-for="tech in item.tech" :key="tech">{{ tech }}</span>
              </div>
            </div>
          </article>
        </div>
      </div>

      <!-- JARVIS LAB -->
      <div id="jarvis" class="jarvis-lab reveal">
        <div class="jarvis-orb-wrap">
          <div class="jarvis-orb">
            <div class="orb-ring orb-ring-one"></div>
            <div class="orb-ring orb-ring-two"></div>
            <div class="orb-core">J</div>
          </div>
        </div>

        <div class="jarvis-copy">
          <span class="eyebrow">04 / PERSONAL AI LAB</span>
          <h3>JARVIS <span>isn't a concept.</span><br />It's an engineering experiment.</h3>
          <p>
            A personalized AI assistant built around OpenClaw, automation,
            model routing, GitHub workflows, Windows execution and a
            security-first infrastructure mindset.
          </p>

          <div class="jarvis-stack">
            <span>OpenClaw</span>
            <span>OmniRoute</span>
            <span>GitHub</span>
            <span>Windows Node</span>
            <span>Automation</span>
            <span>AI Models</span>
          </div>

          <button type="button" class="explore-button" @click="showJarvisDetails = true">
            Inspect system <span>→</span>
          </button>
        </div>

        <div class="jarvis-terminal">
          <div class="terminal-head">
            <span><i></i><i></i><i></i></span>
            <span>jarvis@oracle:~</span>
          </div>
          <div class="terminal-body">
            <p><span>$</span> systemctl status jarvis</p>
            <p class="success">● active (running)</p>
            <p><span>$</span> route --model smart</p>
            <p>→ omni / fallback chain ready</p>
            <p><span>$</span> sync --workspace</p>
            <p class="success">✓ windows node connected</p>
            <p class="cursor-line"><span>$</span> <b></b></p>
          </div>
        </div>
      </div>

      <!-- PRINCIPLES -->
      <div id="principles" class="principles-section reveal">
        <div class="flow-heading">
          <span class="eyebrow">05 / HOW I BUILD</span>
          <h3>Systems first. Noise last.</h3>
        </div>

        <div class="principles-grid">
          <article
            v-for="(principle, index) in principles"
            :key="principle.title"
            class="principle-card"
          >
            <span>0{{ index + 1 }}</span>
            <div class="principle-icon">{{ principle.icon }}</div>
            <h4>{{ principle.title }}</h4>
            <p>{{ principle.description }}</p>
          </article>
        </div>
      </div>

      <!-- INTERACTIVE FOOTER CTA -->
      <div class="living-cta reveal">
        <div class="cta-signal">
          <span></span>
          <span></span>
          <span></span>
        </div>

        <span class="eyebrow">SYSTEM READY</span>
        <h3>Have a problem worth<br /><span>building a system for?</span></h3>
        <p>Let's turn the messy part into something useful.</p>

        <a href="#contact" class="explore-button">
          Start a conversation <span>→</span>
        </a>
      </div>
    </div>

    <!-- CASE STUDY MODAL -->
    <Transition name="modal">
      <div
        v-if="selectedProject"
        class="project-modal"
        role="dialog"
        aria-modal="true"
        :aria-label="selectedProject.name"
        @click.self="selectedProject = null"
      >
        <div class="modal-panel">
          <button
            type="button"
            class="modal-close"
            aria-label="Close project"
            @click="selectedProject = null"
          >
            ×
          </button>

          <span class="eyebrow">{{ selectedProject.type }}</span>
          <h3>{{ selectedProject.name }}</h3>
          <p class="modal-lead">{{ selectedProject.longDescription }}</p>

          <div class="modal-sections">
            <div>
              <span>PROBLEM</span>
              <p>{{ selectedProject.problem }}</p>
            </div>
            <div>
              <span>APPROACH</span>
              <p>{{ selectedProject.approach }}</p>
            </div>
            <div>
              <span>TECHNOLOGY</span>
              <div class="tech-row">
                <span v-for="tech in selectedProject.tech" :key="tech">{{ tech }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </Transition>

    <!-- JARVIS MODAL -->
    <Transition name="modal">
      <div
        v-if="showJarvisDetails"
        class="project-modal"
        role="dialog"
        aria-modal="true"
        @click.self="showJarvisDetails = false"
      >
        <div class="modal-panel jarvis-modal">
          <button
            type="button"
            class="modal-close"
            aria-label="Close Jarvis details"
            @click="showJarvisDetails = false"
          >
            ×
          </button>

          <span class="eyebrow">JARVIS / SYSTEM ARCHITECTURE</span>
          <h3>A small assistant with a serious architecture.</h3>

          <div class="jarvis-architecture">
            <div><b>01</b><span>Telegram / User Interface</span></div>
            <div><b>02</b><span>OpenClaw / Agent Layer</span></div>
            <div><b>03</b><span>OmniRoute / Model Routing</span></div>
            <div><b>04</b><span>Tools / GitHub / Automation</span></div>
            <div><b>05</b><span>Oracle Cloud / Control Plane</span></div>
          </div>

          <p class="modal-lead">
            The experiment is about making a capable personal assistant while
            keeping infrastructure lightweight, inspectable and controlled.
          </p>
        </div>
      </div>
    </Transition>
  </section>
</template>

<script setup>
import { computed, onMounted, onUnmounted, ref } from 'vue'

const command = ref('')
const commandResult = ref('')
const activeNode = ref(0)
const activeFilter = ref('ALL')
const selectedProject = ref(null)
const showJarvisDetails = ref(false)
const currentTime = ref('--:--:--')
const signal = ref(87)
const animatedReports = ref(30)
const animatedBuilds = ref(10)
const systemState = ref('OPERATIONAL')
const signalBars = ref([42, 64, 51, 78, 58, 88, 68, 94, 72, 84, 63, 91])

let clockTimer
let telemetryTimer

const yearsExperience = 4.7

const commands = [
  { label: 'Explore projects', command: 'projects' },
  { label: 'Open Jarvis lab', command: 'jarvis' },
  { label: 'See experience', command: 'experience' },
  { label: 'View architecture', command: 'architecture' },
  { label: 'Contact', command: 'contact' },
]

const flowNodes = [
  {
    icon: '◈',
    name: 'Sources',
    detail: 'DATA',
    description: 'SQL, APIs, Excel, SharePoint and operational systems become the raw signal.'
  },
  {
    icon: '↯',
    name: 'Transform',
    detail: 'ETL',
    description: 'Power Query, SQL and automation shape messy source data into reliable structures.'
  },
  {
    icon: '◇',
    name: 'Model',
    detail: 'SEMANTIC',
    description: 'Star schemas, relationships, measures and business logic create a semantic layer.'
  },
  {
    icon: '▦',
    name: 'Visualize',
    detail: 'BI',
    description: 'Power BI turns the semantic layer into decisions people can actually use.'
  },
  {
    icon: '✦',
    name: 'Intelligence',
    detail: 'AI',
    description: 'Automation and AI extend the system beyond reporting into action.'
  },
]

const projects = [
  {
    name: 'Executive Analytics',
    type: 'POWER BI',
    symbol: 'BI',
    visual: 'visual-blue',
    category: 'POWER BI',
    description: 'Executive reporting systems built for fast decisions and operational visibility.',
    longDescription: 'A portfolio of Power BI work focused on executive summaries, leadership reporting, workforce analytics and operational decision support.',
    problem: 'Business users needed reliable information without repeatedly rebuilding reports or manually interpreting spreadsheets.',
    approach: 'Rebuilt report experiences, improved model and visual performance, and designed views around decision-making workflows.',
    tech: ['Power BI', 'DAX', 'Power Query', 'SQL', 'Data Modeling'],
  },
  {
    name: 'Hotel Revenue',
    type: 'DATA ANALYTICS',
    symbol: 'REV',
    visual: 'visual-purple',
    category: 'DATA',
    description: 'Revenue analysis that turns operational hotel data into actionable insight.',
    longDescription: 'An analytics project exploring hotel revenue performance, operational patterns and business intelligence storytelling.',
    problem: 'Raw operational information is difficult to act on without consistent metrics and contextual visualization.',
    approach: 'Prepared the data, established useful metrics and translated the results into an analytical dashboard.',
    tech: ['Power BI', 'Power Query', 'DAX', 'Analytics'],
  },
  {
    name: 'Maven Pizza',
    type: 'BUSINESS ANALYTICS',
    symbol: 'PZ',
    visual: 'visual-cyan',
    category: 'DATA',
    description: 'Business analysis of orders, products, customer behavior and revenue patterns.',
    longDescription: 'A business intelligence project examining sales patterns and turning transactional data into a practical management view.',
    problem: 'Transaction-level data contains useful patterns, but those patterns are hidden without segmentation and clear KPIs.',
    approach: 'Analyzed orders and products, created meaningful measures and presented the results through a decision-focused dashboard.',
    tech: ['Power BI', 'SQL', 'DAX', 'Business Analysis'],
  },
  {
    name: 'JARVIS',
    type: 'AI SYSTEM',
    symbol: 'AI',
    visual: 'visual-green',
    category: 'AI',
    description: 'Personal AI assistant infrastructure with routing, automation and workstation orchestration.',
    longDescription: 'A security-first personal AI system running on lightweight cloud infrastructure with model routing and controlled Windows execution.',
    problem: 'A useful personal assistant needs tools, memory, automation and execution without turning into an uncontrolled black box.',
    approach: 'Separated orchestration from execution, added model routing, GitHub synchronization and controlled workstation access.',
    tech: ['OpenClaw', 'OmniRoute', 'GitHub', 'Linux', 'Windows'],
  },
  {
    name: 'Vehicle Lifecycle',
    type: 'POWER PLATFORM',
    symbol: 'VL',
    visual: 'visual-orange',
    category: 'AUTOMATION',
    description: 'Dataverse-driven vehicle lifecycle management with model-driven workflows.',
    longDescription: 'A Microsoft Power Platform architecture for vehicle lifecycle operations, moving fragmented operational processes toward a connected system.',
    problem: 'Operational information was distributed across forms, tables and manual processes that needed a unified system.',
    approach: 'Designed Dataverse tables, relationships, choices, model-driven experiences and future customer-facing flows.',
    tech: ['Dataverse', 'Power Apps', 'Power Automate', 'Power Pages'],
  },
  {
    name: 'Automated Pipelines',
    type: 'DATA ENGINEERING',
    symbol: 'ETL',
    visual: 'visual-orange',
    category: 'AUTOMATION',
    description: 'Reusable data transformation patterns for repeatable reporting workflows.',
    longDescription: 'Automation patterns designed to remove repetitive manual preparation from reporting workflows.',
    problem: 'Manual spreadsheet preparation consumes time and introduces inconsistent transformations.',
    approach: 'Converted repeatable preparation logic into structured transformations and reusable pipeline patterns.',
    tech: ['Power Query', 'SQL', 'APIs', 'Automation'],
  },
]

const projectFilters = ['ALL', 'POWER BI', 'DATA', 'AUTOMATION', 'AI']

const filteredProjects = computed(() => {
  if (activeFilter.value === 'ALL') return projects
  return projects.filter(project => project.category === activeFilter.value)
})

const timeline = [
  {
    period: 'JAN 2022 — MAR 2023',
    role: 'TEST / DATA',
    company: 'GlobalStep',
    description: 'Worked across testing and Power BI-oriented delivery, building a foundation in quality, reporting and analytical workflows.',
    tech: ['Testing', 'Power BI', 'Data'],
  },
  {
    period: 'APR 2023 — PRESENT',
    role: 'POWER BI DEVELOPER',
    company: 'Accenture',
    description: 'Building and supporting enterprise reporting experiences across visualization, data modeling, performance and live project delivery.',
    tech: ['Power BI', 'DAX', 'SQL', 'Power Query', 'Fabric'],
  },
]

const principles = [
  {
    icon: '⌁',
    title: 'Model before visual',
    description: 'Good dashboards start with a reliable semantic model. The visual is the last layer, not the foundation.'
  },
  {
    icon: '◌',
    title: 'Automate repetition',
    description: 'If the same person has to repeat the same transformation every week, there is probably a system waiting to be built.'
  },
  {
    icon: '↗',
    title: 'Design for decisions',
    description: 'A metric only matters when somebody can understand it quickly enough to make a better decision.'
  },
  {
    icon: '⌘',
    title: 'Keep systems inspectable',
    description: 'Automation should remain understandable, testable and recoverable instead of becoming an invisible black box.'
  },
]

function scrollTo(id) {
  document.getElementById(id)?.scrollIntoView({
    behavior: 'smooth',
    block: 'start'
  })
}

function runCommand(value) {
  command.value = value

  const actions = {
    projects: () => {
      commandResult.value = 'Opening project explorer…'
      scrollTo('projects')
    },
    jarvis: () => {
      commandResult.value = 'Opening JARVIS lab…'
      scrollTo('jarvis')
    },
    experience: () => {
      commandResult.value = 'Opening engineering journey…'
      scrollTo('experience-timeline')
    },
    architecture: () => {
      commandResult.value = 'Opening data architecture…'
      scrollTo('architecture')
    },
    contact: () => {
      commandResult.value = 'Opening contact channel…'
      scrollTo('contact')
    },
  }

  actions[value.toLowerCase()]?.()

  if (!actions[value.toLowerCase()]) {
    commandResult.value = `Command "${value}" is not mapped yet. Try projects, jarvis, experience or architecture.`
  }
}

function executeCommand() {
  if (!command.value.trim()) {
    commandResult.value = 'Enter a command.'
    return
  }

  runCommand(command.value.trim())
}

function updateClock() {
  currentTime.value = new Date().toLocaleTimeString([], {
    hour12: false
  })
}

function updateTelemetry() {
  signal.value = 82 + Math.floor(Math.random() * 16)
  signalBars.value = signalBars.value.map(() =>
    35 + Math.floor(Math.random() * 65)
  )
}

function handleKeydown(event) {
  const target = event.target
  const typing = target instanceof HTMLInputElement ||
    target instanceof HTMLTextAreaElement ||
    target instanceof HTMLSelectElement

  if ((event.ctrlKey || event.metaKey) && event.key.toLowerCase() === 'k') {
    event.preventDefault()
    document.querySelector('.command-input input')?.focus()
    return
  }

  if (event.key === '/' && !typing) {
    event.preventDefault()
    document.querySelector('.command-input input')?.focus()
  }

  if (event.key === 'Escape') {
    selectedProject.value = null
    showJarvisDetails.value = false
  }
}

onMounted(() => {
  updateClock()
  clockTimer = window.setInterval(updateClock, 1000)
  telemetryTimer = window.setInterval(updateTelemetry, 1600)
  window.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  window.clearInterval(clockTimer)
  window.clearInterval(telemetryTimer)
  window.removeEventListener('keydown', handleKeydown)
})
</script>
