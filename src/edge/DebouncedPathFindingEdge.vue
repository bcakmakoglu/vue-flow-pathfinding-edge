<script lang="ts" setup>
import { CSSProperties } from 'vue'
import { ArrowHeadType, ElementId, Node, Position } from '@braks/vue-flow'
import PathFindingEdge from './PathFindingEdge.vue'

interface EdgeProps {
  nodes: Node[]
  id: ElementId
  source: ElementId
  target: ElementId
  sourceX: number
  sourceY: number
  targetX: number
  targetY: number
  selected?: boolean
  animated?: boolean
  sourcePosition?: Position
  targetPosition?: Position
  label?:
    | string
    | {
        component: any
        props?: any
      }
  labelStyle?: any
  labelShowBg?: boolean
  labelBgStyle?: any
  labelBgPadding?: [number, number]
  labelBgBorderRadius?: number
  style?: CSSProperties
  arrowHeadType?: ArrowHeadType
  markerEndId?: string
  data?: any
  sourceHandleId?: ElementId
  targetHandleId?: ElementId
}

const props = withDefaults(defineProps<EdgeProps>(), {
  selected: false,
  sourcePosition: Position.Bottom,
  targetPosition: Position.Top,
  labelStyle: () => ({}),
  labelShowBg: true,
  labelBgStyle: () => ({}),
})

const storedNodes = computed(() => props.nodes)
const p = computed(() => props)
const nodes = useDebounce(storedNodes, 1000)
const debouncedProps = useDebounce(p, 1000)
</script>
<template>
  <PathFindingEdge :nodes="nodes" v-bind="debouncedProps" />
</template>
