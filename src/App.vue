<template>
  <div id="app">
    <TreeChart
      ref="treechart"
      :data="treedata"
      :childrenKey="'children'"
      :lineColor="'#0006'"
      :lineWidth="1"
      :lineOffset="20"
      :transition="0.3"
      :nodeKey="item => item.data.name"
    >
      <template v-slot="{node, childrenVisible}">
        <div class="my-tree-node" :style="{ background: node.data.bgcolor }">
          <span>{{node.data.name}}</span>
          <span v-if="node.data.editable">
            <button>append</button>
            <button>remove</button>
            <button @click="childrenVisible(true)">showChildren</button>
            <button @click="childrenVisible(false)">hideChildren</button>
          </span>
        </div>
      </template>
    </TreeChart>
  </div>
</template>

<script>
import TreeChart from "./index.js";

export default {
  components: {
    TreeChart
  },
  data() {
    return {
      treedata: {
        data: { name: "root", bgcolor: "#1890ff" },
        children: [
          {
            data: { name: "L1#1", bgcolor: "#1890ff" },
            children: [
              {
                data: { name: "L2#1 - ANJINSHUO", bgcolor: "#1890ff" }
              },
              {
                data: {
                  name: "L2#2 - nerfthisan@163.com",
                  bgcolor: "#f5222d"
                }
              },
              {
                data: { name: "L2#3 - i58000 @ github", bgcolor: "#52c41a" }
              },
              {
                data: { name: "L2#4 - anjs @ npm", bgcolor: "#faad14" }
              }
            ]
          },
          {
            data: {
              name: "L1#2-loooooooooooooong loooooooooooooong ago",
              bgcolor: "#1890ff"
            }
          },
          {
            data: {
              name: "L1#3",
              bgcolor: "#1890ff",
              editable: true
            },
            children: [
              {
                data: { name: "children 1", bgcolor: "#1890ff" }
              },
              {
                data: { name: "children 2", bgcolor: "#1890ff" }
              },
              {
                data: { name: "children 3", bgcolor: "#1890ff" }
              }
            ]
          }
        ]
      }
    };
  }
};
</script>
<style scoped>
.my-tree-node {
  white-space: nowrap;
  background: #fff;
  color: #fff;
  padding: 10px;
  margin: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  border: 1px solid #d9d9d9;
  border-radius: 4px;
}
button {
  border: none;
  border-radius: 4px;
  background: #fff;
  outline: none;
  width: 90px;
  height: 20px;
  margin-left: 10px;
  color: #333;
}
</style>
