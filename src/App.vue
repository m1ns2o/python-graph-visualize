<template>
  <div id="app">
    <h1>Python Graph Visualizer</h1>
    <div class="graph-container" ref="graphContainer"></div>
    <!-- <button @click="addRootNode">Add Root Node</button> -->
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref, reactive } from 'vue';
import * as d3 from 'd3';

interface Node {
  id: number;
  label: string;
  x: number;
  y: number;
  children: Node[];
}

const graphContainer = ref<HTMLElement | null>(null);
const nodes = reactive<Node[]>([]);
let nextId = 0;

const width = 1200;
const height = 900;
const margin = 30;  // Margin to avoid creating nodes near the edges

function promptForLabel(defaultLabel: string): string {
  const label = prompt('Enter label for the node:', defaultLabel);
  return label || defaultLabel;
}

function addRootNode() {
  const x = width / 2;
  const y = height / 2;
  const label = promptForLabel('New Node');
  if (x < margin || x > width - margin || y < margin || y > height - margin) {
    return; // Do not add the node if it's too close to the edges
  }
  nodes.push({
    id: nextId++,
    label,
    x,
    y,
    children: []
  });
  renderGraph();
}

function addChild(node: Node) {
  const x = node.x + 150;
  const y = node.y + 150;
  const label = promptForLabel('New Child');
  if (x < margin || x > width - margin || y < margin || y > height - margin) {
    return; // Do not add the child node if it's too close to the edges
  }
  node.children.push({
    id: nextId++,
    label,
    x,
    y,
    children: []
  });
  renderGraph();
}

function deleteNode(node: Node) {
  nodes.splice(nodes.findIndex(n => n.id === node.id), 1);
  renderGraph();
}

function renderGraph() {
  if (!graphContainer.value) return;

  const tree = d3.tree().size([width - 2 * margin, height - 2 * margin]);
  const root = d3.hierarchy({ children: nodes }, (d) => d.children);
  const treeData = tree(root);

  const svg = d3.select(graphContainer.value)
    .selectAll('svg')
    .data([treeData])
    .join('svg')
    .attr('width', width)
    .attr('height', height);

  const g = svg.selectAll('.node')
    .data(treeData.descendants())
    .join('g')
    .attr('class', 'node')
    .attr('transform', (d) => `translate(${d.x + margin},${d.y + margin})`);

  g.append('circle')
    .attr('r', 15)
    .style('fill', '#fff')
    .style('stroke', '#000')
    .style('stroke-width', '2px');

  g.append('text')
    .attr('dy', '.35em')
    .attr('x', (d) => d.children ? -20 : 20)
    .style('text-anchor', (d) => d.children ? 'end' : 'start')
    .text((d) => d.data.label);

  const link = svg.selectAll('.link')
    .data(treeData.links())
    .join('path')
    .attr('class', 'link')
    .attr('d', (d) => `M${d.source.x + margin},${d.source.y + margin}C${(d.source.x + d.target.x) / 2 + margin},${d.source.y + margin} ${(d.source.x + d.target.x) / 2 + margin},${d.target.y + margin} ${d.target.x + margin},${d.target.y + margin}`);

  g.on('click', (event, d) => {
    addChild(d.data);
  });

  g.on('contextmenu', (event, d) => {
    event.preventDefault();
    deleteNode(d.data);
  });
}

onMounted(() => {
  renderGraph();
});
</script>

<style>
.graph-container {
  width: 1200px;
  height: 900px;
  border: 1px solid #ccc;
}

.link {
  fill: none;
  stroke: #ccc;
  stroke-width: 2px;
}

.node circle {
  fill: #fff;
  stroke: #000;
  stroke-width: 4px;
}

.node text {
  font-size: 25px;
  font-family: sans-serif;
}

#app{
  display: flex;
  flex-direction: column;
  align-items: center;
  /* justify-content: center; */
  height: 100vh;
  h1{
    font-family: sans-serif;
    font-weight: bold;
    font-size: 40px;
    margin-top: 30px;
    margin-bottom: 100px;
  }
}
</style>
