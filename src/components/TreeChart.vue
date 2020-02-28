<template>
  <div class="an-treechart" :style="style">
    <TreeChartNode ref="root" :node="data" v-if="data && Object.keys(data).length !== 0">
      <template v-slot="{node, childrenVisible}">
        <slot :node="node" :childrenVisible="childrenVisible"></slot>
      </template>
    </TreeChartNode>
  </div>
</template>

<script>
import TreeChartNode from "./TreeChartNode";

export default {
  name: "TreeChart",
  components: {
    TreeChartNode
  },
  provide() {
    return {
      nodeKey: this.nodeKey,
      childrenKey: this.childrenKey,

      lineColor: this.lineColor,
      lineOffset: this.lineOffset,
      lineWidth: this.lineWidth,
      transition: this.transition,

      rerender: this.rerender
      // collect: this._collect
    };
  },
  props: {
    data: Object,
    nodeKey: {
      type: String | Number | Function | Boolean,
      default: undefined
    },
    childrenKey: {
      type: String,
      default: "children"
    },
    lineColor: {
      type: String,
      default: "#666"
    },
    lineOffset: {
      type: Number,
      default: 20
    },
    lineWidth: {
      type: Number,
      default: 2
    },
    transition: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      style: {}
      // map: new WeakMap()
    };
  },
  created() {
    // this._collect({
    //   parent: undefined,
    //   node: this.data
    // });
  },
  mounted() {
    this.render();
  },
  methods: {
    rerender() {
      this.$forceUpdate();
      this.$nextTick(() => {
        this.render();
        this.$forceUpdate();
      });
    },
    render() {
      this.$refs.root.render();
      this.style = {
        width: this.$refs.root.blockWidth + "px",
        height: this.$refs.root.blockHeight + "px"
      };
    }
    // _collect(obj) {
    //   this.map.set(obj.node, obj);
    // }
  }
};
</script>

<style scoped>
.an-treechart {
  position: relative;
}
</style>

