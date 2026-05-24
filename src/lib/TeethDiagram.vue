<script setup lang="ts">
import { ref, watch, onMounted, onUnmounted, computed } from "vue";
import teethSelection from "../assets/teeth-selection.svg?raw";

export interface TeethInfo {
  id: string;
  number: string;
  isSelected: boolean;
  event: MouseEvent;
  position: {
    x: number;
    y: number;
  };
}

const props = withDefaults(
  defineProps<{
    defaultSelected?: Record<string, boolean>;
    selectedTeeth?: Record<string, boolean>;
    onChange?: (map: Record<string, boolean>, info: TeethInfo) => void;
    width?: number | string;
    height?: number | string;
    defaultColor?: string;
    hoverColor?: string;
    selectedColor?: string;
  }>(),
  {
    defaultSelected: () => ({}),
    width: 350,
    height: "auto",
    defaultColor: "#9e9e9e",
    hoverColor: "#117CCF",
    selectedColor: "#34C759",
  },
);

const containerRef = ref<HTMLElement | null>(null);
const pathsRef = ref<Record<string, SVGPathElement>>({});
const tooltipRef = ref<HTMLElement | null>(null);

const isControlled = computed(() => props.selectedTeeth !== undefined);

// Internal state (used only for initial load)
const internalSelected = ref<Record<string, boolean>>({});

// Update path colors based on selection
function updatePathColors() {
  const map = internalSelected.value;
  const paths = pathsRef.value;
  const defaultColor = props.defaultColor;
  const selectedColor = props.selectedColor;

  Object.entries(paths).forEach(([id, path]) => {
    const isSel = map[id];
    path.style.fill = isSel ? selectedColor : defaultColor;
  });
}

// Watch for controlled mode changes
watch(
  () => props.selectedTeeth,
  (newVal) => {
    if (isControlled.value && newVal) {
      internalSelected.value = { ...newVal };
      updatePathColors();
    }
  },
  { immediate: true },
);

// Tooltip creation
function createTooltip() {
  const tip = document.createElement("div");
  tip.style.position = "fixed";
  tip.style.padding = "4px 8px";
  tip.style.borderRadius = "4px";
  tip.style.fontSize = "12px";
  tip.style.background = "#000";
  tip.style.color = "#fff";
  tip.style.pointerEvents = "none";
  tip.style.whiteSpace = "nowrap";
  tip.style.zIndex = "99999";
  tip.style.display = "none";

  document.body.appendChild(tip);
  tooltipRef.value = tip;
}

function removeTooltip() {
  if (tooltipRef.value) {
    tooltipRef.value.remove();
    tooltipRef.value = null;
  }
}

// Load SVG and setup event handlers
function loadSVG() {
  if (!containerRef.value) return;

  containerRef.value.innerHTML = teethSelection;
  const svgEl = containerRef.value.querySelector("svg");
  if (!svgEl) return;

  const paths = svgEl.querySelectorAll("path");
  const newPathsRef: Record<string, SVGPathElement> = {};

  paths.forEach((path) => {
    const originalId = path.id;
    const id = `tooth-${originalId}`;

    path.setAttribute("id", id);
    path.style.cursor = "pointer";
    path.style.transition = "fill 0.2s ease";

    newPathsRef[id] = path as SVGPathElement;

    const bbox = (path as SVGPathElement).getBBox();
    const hit = document.createElementNS("http://www.w3.org/2000/svg", "rect");
    hit.setAttribute("x", String(bbox.x - 2));
    hit.setAttribute("y", String(bbox.y - 2));
    hit.setAttribute("width", String(bbox.width + 4));
    hit.setAttribute("height", String(bbox.height + 4));
    hit.setAttribute("fill", "transparent");
    hit.style.cursor = "pointer";

    // Hover handlers
    hit.addEventListener("mouseenter", (e) => {
      const selectedObj = internalSelected.value[id];
      if (!selectedObj) {
        path.style.fill = props.hoverColor;
      }

      const tip = tooltipRef.value;
      if (tip) {
        tip.textContent = `Tooth ${originalId}`;
        tip.style.display = "block";
        tip.style.left = `${e.clientX}px`;
        tip.style.top = `${e.clientY - 20}px`;
      }
    });

    hit.addEventListener("mousemove", (e) => {
      const tip = tooltipRef.value;
      if (tip) {
        tip.style.left = `${e.clientX}px`;
        tip.style.top = `${e.clientY - 20}px`;
      }
    });

    hit.addEventListener("mouseleave", () => {
      const selectedObj = internalSelected.value[id];
      path.style.fill = selectedObj ? props.selectedColor : props.defaultColor;
      if (tooltipRef.value) {
        tooltipRef.value.style.display = "none";
      }
    });

    // Click handler - info mode only (no toggle)
    hit.addEventListener("click", (e) => {
      const isSel = !!internalSelected.value[id];

      const svgRect = svgEl.getBoundingClientRect();
      const pos = {
        x: svgRect.left + bbox.x + bbox.width / 2,
        y: svgRect.top + bbox.y,
      };

      props.onChange?.(internalSelected.value, {
        id,
        number: originalId,
        isSelected: isSel,
        event: e as unknown as MouseEvent,
        position: pos,
      });
    });

    path.parentNode?.insertBefore(hit, path.nextSibling);
  });

  pathsRef.value = newPathsRef;

  // Initialize internal state if not controlled
  if (!isControlled.value) {
    internalSelected.value = { ...props.defaultSelected };
  }

  updatePathColors();
}

onMounted(() => {
  createTooltip();
  loadSVG();
});

onUnmounted(() => {
  removeTooltip();
  pathsRef.value = {};
});

// Sync defaultSelected when not controlled
watch(
  () => props.defaultSelected,
  (newVal) => {
    if (!isControlled.value) {
      internalSelected.value = { ...newVal };
      updatePathColors();
    }
  },
);
</script>

<template>
  <div
    ref="containerRef"
    :style="{
      width: typeof width === 'number' ? `${width}px` : width,
      height: typeof height === 'number' ? `${height}px` : height,
    }"
  />
</template>
