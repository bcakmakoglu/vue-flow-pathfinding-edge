# Vue Flow Pathfinding Edge 🧲

![alt text](https://64.media.tumblr.com/69de98405fbd0ff131c7e34e71e517f4/tumblr_nv4euoaSRu1ufzu8po1_500.gifv)
![top-language](https://img.shields.io/github/languages/top/bcakmakoglu/vue-flow-pathfinding-edge)
[![dependencies Status](https://status.david-dm.org/gh/bcakmakoglu/vue-flow-pathfinding-edge.svg)](https://david-dm.org/bcakmakoglu/vue-flow-pathfinding-edge)
[![devDependencies Status](https://status.david-dm.org/gh/bcakmakoglu/vue-flow-pathfinding-edge.svg?type=dev)](https://david-dm.org/bcakmakoglu/vue-flow-pathfinding-edge?type=dev)
![vulnerabilities](https://img.shields.io/snyk/vulnerabilities/github/bcakmakoglu/vue-flow-pathfinding-edge)
![GitHub code size in bytes](https://img.shields.io/github/languages/code-size/bcakmakoglu/vue-flow-pathfinding-edge)
![GitHub last commit](https://img.shields.io/github/last-commit/bcakmakoglu/vue-flow-pathfinding-edge)

### **Custom Edge that never crosses other Nodes**

Check the [documentation](https://vueflow.dev/docs/addons/pathfinding) for more info on how to use this custom edge.

## 🛠 Setup

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
  useVueFlow
} from '@braks/vue-flow'
import PathFindingEdge from '@braks/vue-flow-pathfinding-edge'
import initialElements from './initial-elements'

const elements = ref<Elements>(initialElements)

// create a new context so we can fetch nodes
const store = useVueFlow({
  edgeTypes: {
    pathFinding: true
  }
})
</script>
<template>
  <div style="height: 300px">
    <VueFlow v-model="elements">
      <template #edge-pathFinding="props">
        <PathFindingEdge :nodes="store.nodes" v-bind="props" />
      </template>
    </VueFlow>
  </div>
</template>
```

```typescript
// initial-elements.ts
export default [
  {
    id: '1',
    data: {
      label: 'Node 1',
    },
    position: {
      x: 430,
      y: 0,
    },
  },
  {
    id: '2',
    data: {
      label: 'Node 2',
    },
    position: {
      x: 230,
      y: 90,
    },
  },
  {
    id: 'e12',
    source: '1',
    target: '2',
    label: 'Smart Edge',
    style: { stroke: 'red' },
    type: 'pathFinding'
  },
]
```
