<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  nodeKey: [String, Number],
  nodeValue: null,
  depth: { type: Number, default: 0 }
})

const isExpanded = ref(true)


const isObject = computed(() => {
  return props.nodeValue !== null && typeof props.nodeValue === 'object'
})


const valType = computed(() => {
  if (props.nodeValue === null) return 'null'
  if (Array.isArray(props.nodeValue)) return 'array'
  return typeof props.nodeValue
})

const toggleNode = () => {
  if (isObject.value) {
    isExpanded.value = !isExpanded.value
  }
}
</script>

<template>
  <div class="tree-node-wrapper" :style="{ marginLeft: `${depth * 16}px` }">
    <div 
      class="node-header" 
      :class="{ 'clickable-branch': isObject, 'is-open': isExpanded && isObject }"
      @click="toggleNode"
    >
      <span v-if="isObject" class="toggle-icon">{{ isExpanded ? '▼' : '▶' }}</span>
      <span v-else class="bullet-icon">•</span>

      <span class="node-key">{{ nodeKey }}:</span>

      <span v-if="!isObject" class="node-value" :class="valType">
        {{ valType === 'string' ? `"${nodeValue}"` : nodeValue }}
      </span>

      <span class="type-badge" :class="valType">
        {{ valType }}
      </span>
    </div>

    <div v-if="isObject && isExpanded" class="node-children">
      <TreeNode 
        v-for="(val, key) in nodeValue" 
        :key="key" 
        :node-key="key" 
        :node-value="val"
        :depth="depth + 1"
      />
    </div>
  </div>
</template>

<style scoped>
.tree-node-wrapper {
  margin: 4px 0;
  font-family: monospace;
  font-size: 13px;
}

.node-header {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 4px 8px;
  border-radius: 4px;
  transition: background-color 0.15s;
  white-space: nowrap;
}

.clickable-branch {
  cursor: pointer;
}

.clickable-branch:hover {
  background-color: #1e293b;
}

.toggle-icon {
  color: #a78bfa;
  font-size: 10px;
  width: 12px;
}

.bullet-icon {
  color: #475569;
  width: 12px;
  text-align: center;
}

.node-key {
  color: #38bdf8;
  font-weight: bold;
}

.node-value.string { color: #34d399; }
.node-value.number { color: #fbbf24; }
.node-value.boolean { color: #f43f5e; }

.type-badge {
  font-size: 10px;
  text-transform: uppercase;
  padding: 1px 5px;
  border-radius: 3px;
  font-weight: bold;
  background-color: #1e293b;
  color: #94a3b8;
}

.type-badge.object { color: #c084fc; background-color: rgba(192, 132, 252, 0.1); }
.type-badge.array { color: #60a5fa; background-color: rgba(96, 165, 250, 0.1); }
</style>