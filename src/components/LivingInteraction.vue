<template>
  <!-- Desktop glass cursor -->
  <div
    class="living-cursor"
    :class="{
      'is-visible': isVisible,
      'is-interactive': isInteractive,
      'is-pressed': isPressed
    }"
    :style="cursorStyle"
    aria-hidden="true"
  >
    <span class="cursor-halo"></span>
    <span class="cursor-core"></span>

    <span v-if="cursorLabel" class="cursor-label">
      {{ cursorLabel }}
    </span>
  </div>

  <!-- Mobile touch energy -->
  <div class="touch-layer" aria-hidden="true">
    <span
      v-for="pulse in pulses"
      :key="pulse.id"
      class="touch-pulse"
      :style="{
        left: `${pulse.x}px`,
        top: `${pulse.y}px`
      }"
    >
      <span class="touch-ring"></span>
      <span class="touch-core"></span>

      <i
        v-for="particle in 6"
        :key="particle"
        class="touch-particle"
        :style="{ '--particle': particle }"
      ></i>
    </span>
  </div>
</template>

<script setup>
import {
  computed,
  onBeforeUnmount,
  onMounted,
  ref
} from "vue"

const isVisible = ref(false)
const isInteractive = ref(false)
const isPressed = ref(false)
const cursorLabel = ref("")

const x = ref(0)
const y = ref(0)

const targetX = ref(0)
const targetY = ref(0)

const pulses = ref([])

let raf = 0
let pulseId = 0
let pressTimer = null
let magneticTargets = []

const cursorStyle = computed(() => ({
  transform:
    `translate3d(${x.value}px, ${y.value}px, 0)`
}))

function getTarget(element) {
  if (!element?.closest) return null

  return element.closest(
    "[data-cursor], [data-magnetic], button, a, .project-card, .flow-node"
  )
}

function updateCursorTarget(element) {
  const target = getTarget(element)

  if (!target) {
    isInteractive.value = false
    cursorLabel.value = ""
    return
  }

  isInteractive.value = true

  if (target.dataset.cursor) {
    cursorLabel.value = target.dataset.cursor
  } else if (
    target.classList.contains("project-card")
  ) {
    cursorLabel.value = "EXPLORE"
  } else if (
    target.classList.contains("flow-node")
  ) {
    cursorLabel.value = "INSPECT"
  } else if (target.tagName === "A") {
    cursorLabel.value = "OPEN"
  } else {
    cursorLabel.value = "SELECT"
  }
}

function updateMagneticTargets(
  clientX,
  clientY,
  activeTarget
) {
  for (const element of magneticTargets) {
    if (element === activeTarget) {
      const rect =
        element.getBoundingClientRect()

      const dx =
        (clientX -
          (rect.left + rect.width / 2)) * 0.06

      const dy =
        (clientY -
          (rect.top + rect.height / 2)) * 0.06

      element.style.setProperty(
        "--mag-x",
        `${dx}px`
      )

      element.style.setProperty(
        "--mag-y",
        `${dy}px`
      )
    } else {
      element.style.setProperty(
        "--mag-x",
        "0px"
      )

      element.style.setProperty(
        "--mag-y",
        "0px"
      )
    }
  }
}

function handlePointerMove(event) {
  if (event.pointerType === "touch") return

  targetX.value = event.clientX
  targetY.value = event.clientY

  isVisible.value = true

  /* Screen-wide light follows the cursor. */
  document.documentElement.style.setProperty(
    "--cursor-x",
    `${event.clientX}px`
  )

  document.documentElement.style.setProperty(
    "--cursor-y",
    `${event.clientY}px`
  )

  const target = getTarget(event.target)

  updateCursorTarget(event.target)

  if (
    target &&
    target.matches(
      "[data-magnetic], button, .project-card"
    )
  ) {
    updateMagneticTargets(
      event.clientX,
      event.clientY,
      target
    )
  } else {
    updateMagneticTargets(
      event.clientX,
      event.clientY,
      null
    )
  }
}

function animateCursor() {
  x.value +=
    (targetX.value - x.value) * 0.17

  y.value +=
    (targetY.value - y.value) * 0.17

  raf = requestAnimationFrame(
    animateCursor
  )
}

function handlePointerDown(event) {
  if (event.pointerType === "touch") {
    createTouchPulse(event)
    return
  }

  isPressed.value = true

  clearTimeout(pressTimer)

  pressTimer = setTimeout(() => {
    isPressed.value = false
  }, 180)
}

function handlePointerUp(event) {
  if (event.pointerType !== "touch") {
    isPressed.value = false
  }
}

function createTouchPulse(event) {
  if (event.pointerType !== "touch") return

  const id = ++pulseId

  pulses.value.push({
    id,
    x: event.clientX,
    y: event.clientY
  })

  window.setTimeout(() => {
    pulses.value =
      pulses.value.filter(
        pulse => pulse.id !== id
      )
  }, 760)
}

function handlePointerLeave() {
  isVisible.value = false
  isInteractive.value = false
  cursorLabel.value = ""

  document.documentElement.style.setProperty(
    "--cursor-x",
    "-1000px"
  )

  document.documentElement.style.setProperty(
    "--cursor-y",
    "-1000px"
  )

  updateMagneticTargets(
    0,
    0,
    null
  )
}

function resetMagneticStyles() {
  for (const element of magneticTargets) {
    element.style.removeProperty("--mag-x")
    element.style.removeProperty("--mag-y")
  }
}

onMounted(() => {
  const finePointer =
    window.matchMedia("(pointer: fine)")

  const reducedMotion =
    window.matchMedia(
      "(prefers-reduced-motion: reduce)"
    )

  magneticTargets =
    Array.from(
      document.querySelectorAll(
        "[data-magnetic], button, .project-card"
      )
    )

  if (
    finePointer.matches &&
    !reducedMotion.matches
  ) {
    window.addEventListener(
      "pointermove",
      handlePointerMove,
      { passive: true }
    )

    window.addEventListener(
      "pointerleave",
      handlePointerLeave,
      { passive: true }
    )

    window.addEventListener(
      "pointerdown",
      handlePointerDown,
      { passive: true }
    )

    window.addEventListener(
      "pointerup",
      handlePointerUp,
      { passive: true }
    )

    raf =
      requestAnimationFrame(
        animateCursor
      )
  } else {
    window.addEventListener(
      "pointerdown",
      handlePointerDown,
      { passive: true }
    )
  }
})

onBeforeUnmount(() => {
  cancelAnimationFrame(raf)

  clearTimeout(pressTimer)

  window.removeEventListener(
    "pointermove",
    handlePointerMove
  )

  window.removeEventListener(
    "pointerleave",
    handlePointerLeave
  )

  window.removeEventListener(
    "pointerdown",
    handlePointerDown
  )

  window.removeEventListener(
    "pointerup",
    handlePointerUp
  )

  resetMagneticStyles()
})
</script>
