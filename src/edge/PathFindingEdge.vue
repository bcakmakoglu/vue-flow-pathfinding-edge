<script lang="ts" setup>
import { CSSProperties } from 'vue'
import {
  getEdgeCenter,
  BezierEdge,
  EdgeText,
  getMarkerEnd,
  ArrowHeadType,
  ElementId,
  Position,
  EdgeProps,
  GraphNode,
} from '@braks/vue-flow'
import { createGrid, gridRatio } from './createGrid'
import { drawSmoothLinePath } from './drawSvgPath'
import { generatePath } from './generatePath'
import { getBoundingBoxes } from './getBoundingBoxes'
import { gridToGraphPoint } from './pointConversion'

interface PathFindingEdgeProps extends EdgeProps {
  nodes: GraphNode[]
  id: ElementId
  source: ElementId
  target: ElementId
  sourceX: number
  sourceY: number
  targetX: number
  targetY: number
  selected?: boolean
  animated?: boolean
  sourcePosition: Position
  targetPosition: Position
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

const props = withDefaults(defineProps<PathFindingEdgeProps>(), {
  nodes: () => [],
  selected: false,
  sourcePosition: Position.Bottom,
  targetPosition: Position.Top,
  labelStyle: () => ({}),
  labelShowBg: true,
  labelBgStyle: () => ({}),
})

const markerEnd = computed(() => getMarkerEnd(props.arrowHeadType, props.markerEndId))
const centered = computed(() =>
  getEdgeCenter({
    ...props,
  }),
)
const source = computed(() => ({
  x: props.sourceX,
  y: props.sourceY,
  position: props.sourcePosition,
}))
const target = computed(() => ({
  x: props.targetX,
  y: props.targetY,
  position: props.targetPosition,
}))

// We use the nodes information to generate bounding boxes for them
// and the graph
const bb = computed(() => getBoundingBoxes(props.nodes, nodePadding, graphPadding, roundCoordinatesTo))

const gridPath = computed(() => {
  let path: number[][] = []

  if (target.value.x && source.value.x && props.nodes.length) {
    // We then can use the grid representation to do pathfinding
    // With this information, we can create a 2D grid representation of
    // our graph, that tells us where in the graph there is a "free" space or not
    const { grid, start, end } = createGrid(bb.value.graph, bb.value.nodes, source.value, target.value)
    path = generatePath(grid, start, end)
  }

  return path
})

const path = computed(() => {
  let svgPath = ''
  if (gridPath.value?.length) {
    // Here we convert the grid path to a sequence of graph coordinates.
    const graphPath = gridPath.value.map((gridPoint) => {
      const [x, y] = gridPoint
      const graphPoint = gridToGraphPoint({ x, y }, bb.value.graph.xMin, bb.value.graph.yMin)
      return [graphPoint.x, graphPoint.y]
    })

    // Finally, we can use the graph path to draw the edge
    svgPath = drawSmoothLinePath(source.value, target.value, graphPath)
  }

  return svgPath
})
const attrs: any = useAttrs()
</script>
<template>
  <BezierEdge v-if="gridPath && gridPath.length <= 2" v-bind="{ ...props, ...attrs }" />
  <template v-else>
    <path :style="{ ...props.style, ...attrs.style }" class="vue-flow__edge-path" :d="path" :marker-end="markerEnd" />
    <EdgeText
      v-if="props.label"
      :x="centered[0]"
      :y="centered[1]"
      :label="props.label"
      :label-style="props.labelStyle"
      :label-show-bg="props.labelShowBg"
      :label-bg-style="props.labelBgStyle"
      :label-bg-padding="props.labelBgPadding"
      :label-bg-border-radius="props.labelBgBorderRadius"
    />
  </template>
</template>
