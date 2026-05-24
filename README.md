# vue-teeth-selector

A lightweight, SVG-based **interactive teeth diagram selector** for Vue 3.

Perfect for dental applications, medical software, EHR/EMR systems, clinical dashboards, or any UI that needs tooth selection.

## Features

- ✔ **Click to select** teeth from an interactive diagram
- ✔ **Hover highlight** with tooltip showing tooth number
- ✔ **Controlled & uncontrolled** state support
- ✔ **Callback** provides selection map + click position
- ✔ **Framework-agnostic** styling works with any UI library
- ✔ **Zero runtime dependencies** (only Vue 3 peer)
- ✔ **SVG-based** for pixel-perfect rendering at any size
- ✔ **Customizable** colors, dimensions, and styling
- ✔ **Lightweight** ~22KB gzipped

## Installation

```bash
npm install vue-teeth-selector
# or
yarn add vue-teeth-selector
```

## Quick Start

```vue
<script setup>
import { ref } from "vue";
import { TeethDiagram } from "vue-teeth-selector";

const selectedTeeth = ref({
  "tooth-11": true,
});

const handleTeethChange = (map, info) => {
  // Toggle selection on click
  if (!info.isSelected) {
    selectedTeeth.value = { ...map, [info.id]: true };
  } else {
    selectedTeeth.value = { ...map, [info.id]: false };
  }
};
</script>

<template>
  <TeethDiagram :selectedTeeth="selectedTeeth" @change="handleTeethChange" />
</template>
```

## Props

| Prop                   | Type               | Default  | Description            |
| ---------------------- | ------------------ | -------- | ---------------------- |
| `selectedTeeth`        | `{[id]: true}`     | —        | Controlled mode        |
| `defaultSelected`      | `{[id]: true}`     | `{}`     | Initial selected teeth |
| `onChange` / `@change` | `function`         | —        | Tooth click callback   |
| `width`                | `number \| string` | `350`    | Component width        |
| `height`               | `number \| string` | `"auto"` | Component height       |

## @Change Event

Emitted when a tooth is clicked. Provides:

```typescript
{
  id: string        // Tooth ID (e.g., "tooth-11")
  number: string    // Tooth number (e.g., "11")
  isSelected: boolean  // Was selected before click
  event: MouseEvent    // Original click event
  position: { x: number, y: number }  // Popup position
}
```

## Local Development

```bash
npm install
npm run dev
```

## Build for Production

```bash
npm run build
```

Outputs to `dist/`:

- `vue-teeth-selector.js` — ES module
- `vue-teeth-selector.umd.cjs` — UMD/CommonJS

## Contributing

Issues and pull requests are welcome!
