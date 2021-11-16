<script lang="ts" setup>
import {
  VueFlow,
  MiniMap,
  Controls,
  Background,
  Connection,
  Edge,
  Elements,
  FlowInstance,
  addEdge,
  removeElements,
} from '@braks/vue-flow'
import initialElements from './elements'
import { PathFindingEdge } from '~/index'

const elements = ref<Elements>(initialElements)
const rfInstance = ref<FlowInstance | null>(null)
const onElementsRemove = (elementsToRemove: Elements) => (elements.value = removeElements(elementsToRemove, elements.value))
const onConnect = (params: Edge | Connection) => (elements.value = addEdge(params, elements.value))
const onLoad = (flowInstance: FlowInstance) => {
  flowInstance.fitView({ padding: 1 })
  rfInstance.value = flowInstance
}
</script>
<template>
  <div style="height: 100%">
    <VueFlow
      class="vue-flow-basic-example"
      :elements="elements"
      :edge-types="{ pathFinding: PathFindingEdge }"
      @elements-remove="onElementsRemove"
      @connect="onConnect"
      @load="onLoad"
    >
      <Controls />
      <MiniMap />
      <Background color="#aaa" :gap="8" />
    </VueFlow>
  </div>
</template>
<style>
@import '../node_modules/@braks/vue-flow/dist/style.css';
@import '../node_modules/@braks/vue-flow/dist/theme-default.css';

html,
body,
#root {
  height: 100%;
  width: 100%;
}
</style>
