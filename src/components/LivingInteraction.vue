<template>
  <div ref="catCursor"
    class="cat-cursor"
    :class="{
      'is-visible': isVisible,
      'is-interactive': isInteractive,
      'is-pressed': isPressed,
      'idle': isIdle
    }"
    :style="cursorStyle"
    aria-hidden="true"
  >
    <svg viewBox="0 0 100 100" class="cat-svg" xmlns="http://www.w3.org/2000/svg">
      <!-- Ears -->
      <path d="M30 30 L20 15 L30 20 Z" fill="#fff" class="cat-ear left-ear"/>
      <path d="M70 30 L80 15 L70 20 Z" fill="#fff" class="cat-ear right-ear"/>
      <!-- Head -->
      <circle cx="50" cy="55" r="30" fill="#fff" stroke="#ddd" stroke-width="2"/>
      <!-- Eyes -->
      <ellipse cx="38" cy="45" rx="5" ry="7" fill="#000" class="cat-eye left-eye" ref="leftEye"/>
      <ellipse cx="62" cy="45" rx="5" ry="7" fill="#000" class="cat-eye right-eye" ref="rightEye"/>
      <circle class="cat-pupil left-pupil" cx="38" cy="45" r="2" fill="#000"/>
      <circle class="cat-pupil right-pupil" cx="62" cy="45" r="2" fill="#000"/>
      <!-- Nose -->
      <circle cx="50" cy="55" r="3" fill="#ffcccb"/>
      <!-- Mouth -->
      <path d="M45 65 Q50 70 55 65" stroke="#000" stroke-width="2" fill="none" class="cat-mouth"/>
      <path d="M45 65 Q50 78 55 65" stroke="#000" stroke-width="2" fill="none" class="cat-mouth open"/>
    </svg>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from "vue"

const isVisible = ref(false)
const isInteractive = ref(false)
const isPressed = ref(false)
const isIdle = ref(false)

const cursorLabel = ref("") // kept for compatibility, unused
const leftEye = ref(null)
const rightEye = ref(null)
const catCursor = ref(null)

const x = ref(0)
const y = ref(0)
const targetX = ref(0)
const targetY = ref(0)
let leftEyeRect = null
let rightEyeRect = null
let prevTime = null
let prevX = null
let prevY = null
let fastMoveTimer = null

let raf = 0
let idleTimeout = null
let pressTimer = null

const cursorStyle = computed(() => ({
  transform: `translate3d(${x.value}px, ${y.value}px, 0) translate(-50%, -50%)`
}))

function getTarget(element) {
  if (!element?.closest) return null
  return element.closest("[data-cursor], [data-magnetic], button, a, .project-card, .flow-node")
}

function updateCursorTarget(element) {
  const target = getTarget(element)
  if (!target) {
    isInteractive.value = false
    cursorLabel.value = ""
    return
  }
  isInteractive.value = true
  if (target.dataset?.cursor) {
    cursorLabel.value = target.dataset.cursor
  } else if (target.classList.contains("project-card")) {
    cursorLabel.value = "EXPLORE"
  } else if (target.classList.contains("flow-node")) {
    cursorLabel.value = "INSPECT"
  } else if (target.tagName === "A") {
    cursorLabel.value = "OPEN"
  } else {
    cursorLabel.value = "SELECT"
  }
}

function handlePointerMove(event) {
  if (event.pointerType === "touch") return

  targetX.value = event.clientX
  targetY.value = event.clientY

  isVisible.value = true
  isIdle.value = false
  if (idleTimeout) clearTimeout(idleTimeout)
  idleTimeout = setTimeout(() => {
    isIdle.value = true
  }, 3000) // idle after 3 seconds of no movement

  document.documentElement.style.setProperty("--cursor-x", `${event.clientX}px`)
  document.documentElement.style.setProperty("--cursor-y", `${event.clientY}px`)

  const target = getTarget(event.target)
  updateCursorTarget(event.target)

  if (target && target.matches("[data-magnetic], button, .project-card")) {
    updateMagneticTargets(event.clientX, event.clientY, target)
  } else {
    updateMagneticTargets(event.clientX, event.clientY, null)
  }

  // ---- Eye tracking ----
  if (leftEyeRect && rightEyeRect && catCursor.value) {
    const computePupil = (rect, isLeft) => {
      const eyeCenterX = rect.left + rect.width / 2
      const eyeCenterY = rect.top + rect.height / 2
      let offsetX = ((event.clientX - eyeCenterX) / rect.width) * 3
      let offsetY = ((event.clientY - eyeCenterY) / rect.height) * 3
      const dist = Math.hypot(offsetX, offsetY)
      if (dist > 3) {
        const scale = 3 / dist
        offsetX *= scale
        offsetY *= scale
      }
      const varX = isLeft ? '--pupil-x-left' : '--pupil-x-right'
      const varY = isLeft ? '--pupil-y-left' : '--pupil-y-right'
      catCursor.value.style.setProperty(varX, `${offsetX}px`)
      catCursor.value.style.setProperty(varY, `${offsetY}px`)
    }
    computePupil(leftEyeRect, true)
    computePupil(rightEyeRect, false)
  }

  // ---- Fast movement detection ----
  const now = performance.now()
  if (prevTime !== null) {
    const dt = now - prevTime
    const dx = event.clientX - prevX
    const dy = event.clientY - prevY
    const distance = Math.hypot(dx, dy)
    const speed = distance / dt // px per ms
    if (speed > 0.5 && catCursor.value) {
      catCursor.value.classList.add('fast-move')
      if (fastMoveTimer) clearTimeout(fastMoveTimer)
      fastMoveTimer = setTimeout(() => {
        catCursor.value?.classList.remove('fast-move')
      }, 200)
    }
  }
  prevTime = now
  prevX = event.clientX
  prevY = event.clientY
}

function animateCursor() {
  x.value += (targetX.value - x.value) * 0.15
  y.value += (targetY.value - y.value) * 0.15
  raf = requestAnimationFrame(animateCursor)
}

let magneticTargets = []
function updateMagneticTargets(clientX, clientY, activeTarget) {
  for (const element of magneticTargets) {
    if (element === activeTarget) {
      const rect = element.getBoundingClientRect()
      const dx = (clientX - (rect.left + rect.width / 2)) * 0.06
      const dy = (clientY - (rect.top + rect.height / 2)) * 0.06
      element.style.setProperty("--mag-x", `${dx}px`)
      element.style.setProperty("--mag-y", `${dy}px`)
    } else {
      element.style.setProperty("--mag-x", "0px")
      element.style.setProperty("--mag-y", "0px")
    }
  }
}

function handlePointerDown(event) {
  // Show cat and move to click/touch location
  isVisible.value = true
  isIdle.value = false
  if (idleTimeout) clearTimeout(idleTimeout)
  // Set target to event position
  targetX.value = event.clientX
  targetY.value = event.clientY

  // Click reaction
  isPressed.value = true
  clearTimeout(pressTimer)
  pressTimer = setTimeout(() => {
    isPressed.value = false
  }, 180)

  // ---- Click/Tap reaction enhancements ----
  if (catCursor.value) {
    catCursor.value.classList.add('mouth-open')
    catCursor.value.classList.add('blink-on-click')
    catCursor.value.classList.add('cat-ear-click')
    setTimeout(() => {
      catCursor.value?.classList.remove('mouth-open')
      catCursor.value?.classList.remove('blink-on-click')
      catCursor.value?.classList.remove('cat-ear-click')
    }, 300)
  }
}

function handlePointerUp(event) {
  if (event.pointerType !== "touch") {
    isPressed.value = false
  }
}

function handlePointerLeave() {
  isVisible.value = false
  isInteractive.value = false
  cursorLabel.value = ""
  isIdle.value = false
  if (idleTimeout) clearTimeout(idleTimeout)

  document.documentElement.style.setProperty("--cursor-x", "-1000px")
  document.documentElement.style.setProperty("--cursor-y", "-1000px")
  updateMagneticTargets(0, 0, null)

  // Reset pupil CSS variables
  if (catCursor.value) {
    catCursor.value.style.setProperty('--pupil-x-left', '0px')
    catCursor.value.style.setProperty('--pupil-y-left', '0px')
    catCursor.value.style.setProperty('--pupil-x-right', '0px')
    catCursor.value.style.setProperty('--pupil-y-right', '0px')
  }
}

function resetMagneticStyles() {
  for (const element of magneticTargets) {
    element.style.removeProperty("--mag-x")
    element.style.removeProperty("--mag-y")
  }
}

onMounted(() => {
  const finePointer = window.matchMedia("(pointer: fine)")
  const reducedMotion = window.matchMedia("(prefers-reduced-motion: reduce)")

  magneticTargets = Array.from(
    document.querySelectorAll("[data-magnetic], button, .project-card")
  )

  if (finePointer.matches && !reducedMotion.matches) {
    window.addEventListener("pointermove", handlePointerMove, { passive: true })
    window.addEventListener("pointerleave", handlePointerLeave, { passive: true })
    window.addEventListener("pointerdown", handlePointerDown, { passive: true })
    window.addEventListener("pointerup", handlePointerUp, { passive: true })
    raf = requestAnimationFrame(animateCursor)
  } else {
    // Coarse pointers or reduced motion – only show cat on click
    window.addEventListener("pointerdown", handlePointerDown, { passive: true })
  }

  // Capture eye bounding rectangles for pupil calculations (once DOM is ready)
  if (leftEye.value && rightEye.value) {
    leftEyeRect = leftEye.value.getBoundingClientRect()
    rightEyeRect = rightEye.value.getBoundingClientRect()
  }
})

onBeforeUnmount(() => {
  cancelAnimationFrame(raf)
  clearTimeout(pressTimer)
  if (idleTimeout) clearTimeout(idleTimeout)
  if (fastMoveTimer) clearTimeout(fastMoveTimer)

  window.removeEventListener("pointermove", handlePointerMove)
  window.removeEventListener("pointerleave", handlePointerLeave)
  window.removeEventListener("pointerdown", handlePointerDown)
  window.removeEventListener("pointerup", handlePointerUp)

  resetMagneticStyles()
})
</script>

<style scoped>
.cat-cursor {
  position: fixed;
  top: 0;
  left: 0;
  width: 80px;
  height: 80px;
  pointer-events: none;
  will-change: transform;
  opacity: 0;
  transition: opacity 0.2s ease;
  transform: translate(-50%, -50%);
  z-index: 9999;
}
.cat-cursor.is-visible {
  opacity: 1;
}

/* Responsive scaling */
@media (max-width: 1200px) {
  .cat-cursor {
    width: 70px;
    height: 70px;
  }
}
@media (max-width: 768px) {
  .cat-cursor {
    width: 60px;
    height: 60px;
  }
}
@media (max-width: 480px) {
  .cat-cursor {
    width: 45px;
    height: 45px;
  }
}

.cat-svg {
  width: 100%;
  height: 100%;
}

/* Pupil movement */
.cat-pupil.left-pupil {
  transform: translate(var(--pupil-x-left, 0px), var(--pupil-y-left, 0px));
}
.cat-pupil.right-pupil {
  transform: translate(var(--pupil-x-right, 0px), var(--pupil-y-right, 0px));
}

/* Breathing animation when idle */
@keyframes cat-breath {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.04); }
}

/* Idle ear wiggle */
@keyframes cat-ear-wiggle-idle {
  0%, 100% { transform: rotate(0deg); }
  50% { transform: rotate(3deg); }
}

/* Head bobbing */
@keyframes cat-head-bob {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-3px); }
}

/* Apply idle animations */
.cat-cursor.idle .cat-svg {
  animation: cat-breath 5s ease-in-out infinite, cat-head-bob 5s ease-in-out infinite;
}
.cat-cursor.idle .cat-ear {
  animation: cat-ear-wiggle-idle 2s ease-in-out infinite;
}

/* Blink animation for eyes */
@keyframes cat-blink {
  0%, 90%, 100% { transform: scaleY(1); }
  95% { transform: scaleY(0); }
}
.cat-eye {
  transform-origin: center;
  animation: cat-blink 4s steps(1, start) infinite;
}

/* Ear wiggle on interactive hover */
@keyframes cat-ear-wiggle {
  0%, 100% { transform: rotate(0deg); }
  50% { transform: rotate(5deg); }
}
.cat-cursor.is-interactive .cat-ear {
  animation: cat-ear-wiggle 2s ease-in-out infinite;
}

/* Click reaction – quick head bounce */
@keyframes cat-click-bounce {
  0%, 100% { transform: translateY(0); }
  30% { transform: translateY(-5px); }
  60% { transform: translateY(3px); }
}
.cat-cursor.is-pressed .cat-svg {
  animation: cat-click-bounce 0.3s ease-out;
}

/* Fast movement squash */
@keyframes fast-squash {
  0% { transform: scale(1); }
  50% { transform: scale(1.1, 0.9); }
  100% { transform: scale(1); }
}
.cat-cursor.fast-move .cat-svg {
  animation: fast-squash 0.2s ease-out;
}

/* Mouth open reaction */
.cat-mouth.open {
  opacity: 0;
  transition: opacity 0.1s;
}
.cat-cursor.mouth-open .cat-mouth.open {
  opacity: 1;
}

/* Click blink shortcut */
@keyframes cat-blink-short {
  0%, 100% { transform: scaleY(1); }
  50% { transform: scaleY(0); }
}
.cat-cursor.blink-on-click .cat-eye {
  animation: cat-blink-short 0.3s steps(1, start) forwards;
}

/* Ear click wiggle */
@keyframes cat-ear-wiggle-click {
  0%, 100% { transform: rotate(0deg); }
  50% { transform: rotate(10deg); }
}
.cat-cursor.cat-ear-click .cat-ear {
  animation: cat-ear-wiggle-click 0.3s ease-in-out;
}

/* Reduced motion – disable animations */
@media (prefers-reduced-motion: reduce) {
  .cat-cursor.idle .cat-svg,
  .cat-eye,
  .cat-cursor.is-interactive .cat-ear,
  .cat-cursor.is-pressed .cat-svg,
  .cat-cursor.fast-move .cat-svg,
  .cat-cursor.blink-on-click .cat-eye,
  .cat-cursor.cat-ear-click .cat-ear,
  .cat-cursor.idle .cat-ear {
    animation: none !important;
  }
}
</style>
