<template>
  <div class="editor">
    <VueFlow
      v-model:nodes="nodes"
      v-model:edges="edges"
      class="flow"
      @connect="onConnect"
    >
      <Background />
      <MiniMap />
      <Controls />
    </VueFlow>
    <div class="sidebar">
      <button @click="addNode">Add Node</button>
      <button @click="exportFlow">Export</button>
      <button @click="importFlow">Import</button>
      <textarea v-model="flowJson" placeholder="Flow JSON"></textarea>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { VueFlow, useVueFlow } from '@vue-flow/core'
import { Background } from '@vue-flow/background'
import { Controls } from '@vue-flow/controls'
import { MiniMap } from '@vue-flow/minimap'
import '@vue-flow/core/dist/style.css'
import '@vue-flow/core/dist/theme-default.css'
import '@vue-flow/controls/dist/style.css'
import '@vue-flow/minimap/dist/style.css'

const nodes = ref([
  { id: '1', label: 'Start', position: { x: 0, y: 0 }, type: 'input' }
])
const edges = ref([])

const nodeId = ref(2)

function addNode() {
  nodes.value.push({
    id: String(nodeId.value++),
    label: `Node ${nodeId.value - 1}`,
    position: { x: Math.random() * 250, y: Math.random() * 200 }
  })
}

const { addEdges } = useVueFlow()

function onConnect(params) {
  addEdges([params])
}

const flowJson = ref('')

function exportFlow() {
  flowJson.value = JSON.stringify({ nodes: nodes.value, edges: edges.value }, null, 2)
}

function importFlow() {
  try {
    const parsed = JSON.parse(flowJson.value)
    nodes.value = parsed.nodes || []
    edges.value = parsed.edges || []
  } catch (err) {
    alert('Invalid JSON')
  }
}
</script>

<style>
.editor {
  display: flex;
  height: 100vh;
}
.flow {
  flex: 1;
  height: 100%;
}
.sidebar {
  width: 300px;
  padding: 1rem;
  background: #f7f7f7;
  box-sizing: border-box;
}
.sidebar textarea {
  width: 100%;
  height: 200px;
  margin-top: 0.5rem;
}
</style>
