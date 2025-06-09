<template>
  <div class="editor">
    <VueFlow
      v-model:nodes="nodes"
      v-model:edges="edges"
      class="flow"
      @connect="onConnect"
      @node-click="onNodeClick"
    >
      <Background />
      <MiniMap />
      <Controls />
    </VueFlow>
    <div class="sidebar">
      <button @click="addNode">Add Node</button>
      <button @click="exportFlow">Export</button>
      <button @click="importFlow">Import</button>
      <button @click="runFlow">Run</button>
      <textarea v-model="flowJson" placeholder="Flow JSON"></textarea>
      <div v-if="selectedNode" class="node-editor">
        <h3>Node {{ selectedNode.id }}</h3>
        <label>
          Label
          <input v-model="selectedNode.label" />
        </label>
        <label>
          Script
          <textarea v-model="selectedNode.data.script" />
        </label>
      </div>
      <div v-if="runResult" class="result">
        <h3>Run Result</h3>
        <pre>{{ runResult }}</pre>
      </div>
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
  { id: '1', label: 'Start', position: { x: 0, y: 0 }, type: 'input', data: { script: 'return input' } }
])
const edges = ref([])

const nodeId = ref(2)
const selectedNode = ref(null)
const runResult = ref('')

function addNode() {
  nodes.value.push({
    id: String(nodeId.value++),
    label: `Node ${nodeId.value - 1}`,
    position: { x: Math.random() * 250, y: Math.random() * 200 },
    data: { script: 'return input' }
  })
}

const { addEdges } = useVueFlow()

function onConnect(params) {
  addEdges([params])
}

function onNodeClick(_, node) {
  selectedNode.value = node
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

function runFlow() {
  try {
    let output = null
    for (const node of nodes.value) {
      const script = node.data?.script || 'return input'
      const fn = new Function('input', script)
      output = fn(output)
    }
    runResult.value = JSON.stringify(output, null, 2)
  } catch (err) {
    runResult.value = 'Error: ' + err.message
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
.node-editor label {
  display: block;
  margin-top: 1rem;
}
.result pre {
  background: #eee;
  padding: 0.5rem;
}
</style>
