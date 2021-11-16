<script lang="ts" setup>
import { CSSProperties } from 'vue'
import { BezierEdge, getMarkerEnd, ArrowHeadType, ElementId, Position, Node } from '@braks/vue-flow'
import { createGrid, PointInfo, gridRatio } from './createGrid'
import { drawSmoothLinePath } from './drawSvgPath'
import { generatePath } from './generatePath'
import { getBoundingBoxes } from './getBoundingBoxes'
import { gridToGraphPoint } from './pointConversion'

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

const nodePadding = 10
const graphPadding = 20
const roundCoordinatesTo = gridRatio

const props = withDefaults(defineProps<EdgeProps>(), {
  selected: false,
  sourcePosition: Position.Bottom,
  targetPosition: Position.Top,
  labelStyle: () => ({}),
  labelShowBg: true,
  labelBgStyle: () => ({}),
})

// We use the node's information to generate bounding boxes for them
// and the graph
const bb = computed(() => getBoundingBoxes(props.nodes, nodePadding, graphPadding, roundCoordinatesTo))
const source = computed<PointInfo>(() => ({
  x: props.sourceX,
  y: props.sourceY,
  position: props.sourcePosition,
}))
const target = computed<PointInfo>(() => ({
  x: props.targetX,
  y: props.targetY,
  position: props.targetPosition,
}))

// We then can use the grid representation to do pathfinding
const gridPath = computed(() => {
  if (source.value.x && target.value.x) {
    // With this information, we can create a 2D grid representation of
    // our graph, that tells us where in the graph there is a "free" space or not
    const g = createGrid(bb.value.graph, bb.value.nodes, source.value, target.value)
    return generatePath(g.grid, g.start, g.end)
  } else {
    return [] as any[]
  }
})

// Here we convert the grid path to a sequence of graph coordinates.
const graphPath = computed(() =>
  gridPath.value?.map((gridPoint) => {
    const [x, y] = gridPoint
    const graphPoint = gridToGraphPoint({ x, y }, bb.value.graph.xMin, bb.value.graph.yMin)
    return [graphPoint.x, graphPoint.y]
  }),
)

// Finally, we can use the graph path to draw the edge
const svgPathString = computed(() => drawSmoothLinePath(source.value, target.value, graphPath.value))
const markerEnd = computed(() => getMarkerEnd(props.arrowHeadType, props.markerEndId))
</script>
<template>
  <BezierEdge v-if="gridPath.length <= 2" v-bind="props" />
  <path :style="props.style" class="vue-flow__edge-path" :d="svgPathString" :marker-end="markerEnd" />
</template>
