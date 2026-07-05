<script setup>
import { ref, computed } from 'vue'
import TreeNode from './components/TreeNode.vue'


const initialJson = {
  service: "devstream-cluster-core",
  version: 2.4,
  operational: true,
  metadata: {
    region: "us-east-1",
    activeNodes: ["auth-matrix", "billing-gateway"],
    hardware: {
      cores: 16,
      isolation: null
    }
  }
}

const rawJsonInput = ref(JSON.stringify(initialJson, null, 2))


const parsedHierarchyData = computed(() => {
  if (!rawJsonInput.value.trim()) {
    return { error: "Waiting for text input buffer stream...", data: null }
  }

  try {
    const parsed = JSON.parse(rawJsonInput.value)
    return { error: null, data: parsed }
  } catch (err) {
    return { error: `Syntactic Data Token Error: ${err.message}`, data: null }
  }
})


const dataTelemetryMetrics = computed(() => {
  if (parsedHierarchyData.value.error || !parsedHierarchyData.value.data) {
    return { keyCount: 0, rawBytes: 0 }
  }
  
  const targetObj = parsedHierarchyData.value.data
  const totalKeys = Object.keys(targetObj).length
  const byteCount = new Blob([rawJsonInput.value]).size

  return { keyCount: totalKeys, rawBytes: byteCount }
})
</script>

<template>
  <div class="app-wrapper">
    
    <header class="app-header">
      <h1> DevTree Object Graph Studio</h1>
      <p>An interactive Vue 3 workbench engineered to compile complex object strings into dynamic recursive AST visual hierarchy graphs.</p>
    </header>

    <main class="studio-desk">
      
      <section class="control-panel">
        <div class="panel-header">
          <h3>JSON Code Buffer Ingestion</h3>
          <span class="metrics-badge">{{ dataTelemetryMetrics.rawBytes }} Bytes</span>
        </div>

        <textarea 
          v-model="rawJsonInput" 
          placeholder="Paste standard JSON properties database payload..."
          class="json-code-editor"
        ></textarea>

        <div v-if="parsedHierarchyData.error" class="error-alert-tray">
          {{ parsedHierarchyData.error }}
        </div>
      </section>

      <section class="visualizer-panel">
        <h3>Compiled AST Hierarchy Graph Vector</h3>
        
        <div class="graph-canvas-box">
          <div v-if="parsedHierarchyData.data" class="tree-root-box">
            <TreeNode 
              v-for="(val, key) in parsedHierarchyData.data" 
              :key="key" 
              :node-key="key" 
              :node-value="val" 
            />
          </div>
          <p v-else class="standby-text">
             Standby interface telemetry. Fix syntax structural errors to compile graph nodes.
          </p>
        </div>
      </section>

    </main>
  </div>
</template>

<style scoped>
.app-wrapper {
  max-width: 1200px;
  margin: 40px auto;
  padding: 0 24px;
  font-family: system-ui, -apple-system, sans-serif;
  color: #f8fafc;
}

.app-header {
  border-bottom: 2px solid #1e293b;
  padding-bottom: 20px;
  margin-bottom: 35px;
}

.app-header h1 {
  margin: 0;
  font-size: 30px;
  color: #c084fc;
  letter-spacing: -0.5px;
}

.app-header p {
  margin: 4px 0 0 0;
  color: #64748b;
  font-size: 14px;
}

.studio-desk {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(360px, 1fr));
  gap: 40px;
}

.control-panel {
  background-color: #0f172a;
  border: 1px solid #1e293b;
  padding: 25px;
  border-radius: 16px;
  box-shadow: 0 10px 15px -3px rgba(0,0,0,0.3);
  display: flex;
  flex-direction: column;
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  border-bottom: 1px solid #1e293b;
  padding-bottom: 12px;
}

.panel-header h3 {
  margin: 0;
  font-size: 14px;
  color: #64748b;
  text-transform: uppercase;
}

.metrics-badge {
  font-size: 11px;
  font-family: monospace;
  font-weight: bold;
  color: #c084fc;
  background-color: rgba(192, 132, 252, 0.1);
  padding: 4px 10px;
  border-radius: 6px;
  border: 1px solid rgba(192, 132, 252, 0.2);
}

.json-code-editor {
  width: 100%;
  height: 350px;
  background-color: #020617;
  border: 1px solid #1e293b;
  border-radius: 12px;
  padding: 20px;
  color: #cbd5e1;
  font-family: monospace;
  font-size: 13px;
  line-height: 1.6;
  resize: vertical;
  outline: none;
  box-sizing: border-box;
}

.json-code-editor:focus {
  border-color: #c084fc;
}

.error-alert-tray {
  margin-top: 15px;
  background-color: rgba(239, 68, 68, 0.05);
  border-left: 4px solid #ef4444;
  padding: 12px 16px;
  border-radius: 0 8px 8px 0;
  color: #f87171;
  font-family: monospace;
  font-size: 12px;
  line-height: 1.5;
}

.visualizer-panel {
  display: flex;
  flex-direction: column;
}

.visualizer-panel h3 {
  margin-top: 0;
  margin-bottom: 15px;
  font-size: 14px;
  color: #64748b;
  text-transform: uppercase;
}

.graph-canvas-box {
  background-color: #020617;
  border: 1px dashed #334155;
  border-radius: 16px;
  padding: 25px;
  flex-grow: 1;
  overflow: auto;
  min-height: 350px;
}

.standby-text {
  font-family: monospace;
  font-size: 12px;
  color: #475569;
  text-align: center;
  margin-top: 40px;
}
</style>