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
      <template v-slot:node="{node}">
        <div class="my-tree-node" :class="node.data.type">{{node.data.name}}</div>
      </template>
    </TreeChart>
  </div>
</template>

<script>
import TreeChart from "../dist/vue-treechart.js";

export default {
  components: {
    TreeChart
  },
  data() {
    return {
      treedata: {
        data: { name: "root", type: "primary" },
        children: [
          {
            data: { name: "L1#1", type: "primary" },
            children: [
              {
                data: { name: "L2#1", type: "primary" }
              },
              {
                data: { name: "L2#2", type: "error" }
              },
              {
                data: { name: "L2#3", type: "success" }
              },
              {
                data: { name: "L2#4", type: "warning" }
              }
            ]
          },
          {
            data: {
              name: "L1#2",
              type: "primary"
            }
          },
          {
            data: {
              name: "L1#3",
              type: "primary"
            }
          }
        ]
      }
    };
  }
};
</script>
<style scoped>
.my-tree-node {
  background: #fff;
  padding: 10px;
  margin: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  border: 1px solid #d9d9d9;
  border-radius: 4px;
}
.success {
  color: #fff;
  background: #52c41a;
}
.warning {
  color: #fff;
  background: #faad14;
}
.error {
  color: #fff;
  background: #f5222d;
}
.primary {
  color: #fff;
  background: #1890ff;
}
</style>
