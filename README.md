# Vue Flow Pathfinding Edge 🧲

![alt text](https://64.media.tumblr.com/69de98405fbd0ff131c7e34e71e517f4/tumblr_nv4euoaSRu1ufzu8po1_500.gifv)
![top-language](https://img.shields.io/github/languages/top/bcakmakoglu/vue-flow-pathfinding-edge)
[![dependencies Status](https://status.david-dm.org/gh/bcakmakoglu/vue-flow-pathfinding-edge.svg)](https://david-dm.org/bcakmakoglu/vue-flow-pathfinding-edge)
[![devDependencies Status](https://status.david-dm.org/gh/bcakmakoglu/vue-flow-pathfinding-edge.svg?type=dev)](https://david-dm.org/bcakmakoglu/vue-flow-pathfinding-edge?type=dev)
![vulnerabilities](https://img.shields.io/snyk/vulnerabilities/github/bcakmakoglu/vue-flow-pathfinding-edge)
![GitHub code size in bytes](https://img.shields.io/github/languages/code-size/bcakmakoglu/vue-flow-pathfinding-edge)
![GitHub last commit](https://img.shields.io/github/last-commit/bcakmakoglu/vue-flow-pathfinding-edge)

### **Custom Edge that never crosses other Nodes**

## 🛠 Setup
Some info on setup here.

```bash
# install
$ yarn add @braks/vue-flow-pathfinding-edge

# or
$ npm i --save @braks/vue-flow-pathfinding-edge
```

## 🎮 Quickstart

```vue
<script lang="ts" setup>
import {
  VueFlow,
  Elements,
} from '@braks/vue-flow'
import { PathFindingEdge } from '@braks/vue-flow-pathfinding-edge'
import initialElements from './elements'

const elements = ref<Elements>(initialElements)
</script>
<template>
  <div style="height: 100%">
    <VueFlow
      class="vue-flow-basic-example"
      :elements="elements"
      :edge-types="{ pathFinding: PathFindingEdge }"
    />
  </div>
</template>
```
