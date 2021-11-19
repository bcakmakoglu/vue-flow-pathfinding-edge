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
  useVueFlow,
} from '@braks/vue-flow'
import { createGrid, gridRatio } from './createGrid'
import { drawSmoothLinePath } from './drawSvgPath'
import { generatePath } from './generatePath'
import { getBoundingBoxes } from './getBoundingBoxes'
import { gridToGraphPoint } from './pointConversion'

interface PathFindingEdgeProps extends EdgeProps {
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
  selected: false,
  sourcePosition: Position.Bottom,
  targetPosition: Position.Top,
  labelStyle: () => ({}),
  labelShowBg: true,
  labelBgStyle: () => ({}),
})

const store = useVueFlow()
const markerEnd = computed(() => getMarkerEnd(props.arrowHeadType, props.markerEndId))
const centered = computed(() =>
  getEdgeCenter({
    ...props,
  }),
)

const path = computed(() => {
  // We use the node's information to generate bounding boxes for them
  // and the graph
  const bb = getBoundingBoxes(store.nodes, nodePadding, graphPadding, roundCoordinatesTo)
  const source = {
    x: props.sourceX,
    y: props.sourceY,
    position: props.sourcePosition,
  }
  const target = {
    x: props.targetX,
    y: props.targetY,
    position: props.targetPosition,
  }

  // We then can use the grid representation to do pathfinding
  let gridPath: any[] = []
  // With this information, we can create a 2D grid representation of
  // our graph, that tells us where in the graph there is a "free" space or not
  const g = createGrid(bb.graph, bb.nodes, source, target)
  gridPath = generatePath(g.grid, g.start, g.end)

  // Here we convert the grid path to a sequence of graph coordinates.
  const graphPath = gridPath?.map((gridPoint) => {
    const [x, y] = gridPoint
    const graphPoint = gridToGraphPoint({ x, y }, bb.graph.xMin, bb.graph.yMin)
    return [graphPoint.x, graphPoint.y]
  })

  // Finally, we can use the graph path to draw the edge
  const svgPathString = drawSmoothLinePath(source, target, graphPath)

  return {
    gridPath,
    svgPathString,
  }
})
const attrs = useAttrs()
</script>
<template>
  <BezierEdge v-if="path.gridPath.length <= 2" v-bind="{ ...props, ...attrs }" />
  <template v-else>
    <path
      :style="{ ...props.style, ...attrs.style }"
      class="vue-flow__edge-path"
      :d="path.svgPathString"
      :marker-end="markerEnd"
    />
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
