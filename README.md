# vue-teeth-selector

A lightweight, SVG-based **interactive teeth diagram selector** for Vue 3.

Perfect for dental applications, medical software, EHR/EMR systems, clinical dashboards, or any UI that needs tooth selection.

## Features

✔ Select teeth by clicking  
✔ Hover highlight & tooltip  
✔ Supports controlled & uncontrolled states  
✔ Callback gives selected map + popup position  
✔ Works with any UI library  
✔ No dependencies (only Vue 3)  
✔ SVG-based, pixel-perfect diagram  
✔ Customizable size & styling  
✔ Lightweight and fast

## Installation

```bash
npm install vue-teeth-selector
or
yarn add vue-teeth-selector
```

## Quick Usage

```bash
<script setup>
import { ref } from 'vue'
import { TeethDiagram } from 'vue-teeth-selector'

const selectedTeeth = ref({
  'tooth-11': true,
})

const handleTeethChange = (map, info) => {
  if (!info.isSelected) {
    selectedTeeth.value = { ...map, [info.id]: true }
  } else {
    selectedTeeth.value = { ...map, [info.id]: false }
  }
}
</script>

<template>
  <TeethDiagram
    :selectedTeeth="selectedTeeth"
    @change="handleTeethChange"
  />
</template>
```

## Props

| Prop            | Type           | Default  | Description            |
| --------------- | -------------- | -------- | ---------------------- |
| selectedTeeth   | `{[id]: true}` | —        | Controlled mode        |
| defaultSelected | `{[id]: true}` | `{}`     | Initial selected teeth |
| onChange        | function       | —        | Tooth click callback   |
| width           | number/string  | `350`    | Component width        |
| height          | number/string  | `"auto"` | Component height       |

## Event

The `@change` event provides:

- `map` - Current selection map `{[toothId]: true}`
- `info` - Click info object containing:
  - `id` - Tooth ID (e.g., "tooth-11")
  - `number` - Original tooth number (e.g., "11")
  - `isSelected` - Whether tooth was selected before click
  - `event` - Original mouse event
  - `position` - {x, y} position relative to SVG

## Local Development

```bash
npm install
npm run dev
```

## Build library

```bash
npm run build
```

## Contributing

PRs, issues, and suggestions are welcome!
