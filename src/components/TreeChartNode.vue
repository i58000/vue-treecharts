<template>
  <div class="an-treechart-node" :style="style">
    <slot name="node" :node="node">EMPTY-NODE</slot>
    <div
      class="an-treechart-node__children"
      v-if="node[childrenKey] && node[childrenKey].length > 0"
    >
      <TreeChartNode
        ref="children"
        v-for="(item, index) in node[childrenKey]"
        :key="_nodeKey(item, index)"
        :node="item"
      >
        <template v-slot:node="{node}">
          <slot name="node" :node="node"></slot>
        </template>
      </TreeChartNode>
    </div>
    <svg
      class="an-treechart-node__line"
      width="100%"
      height="100%"
      :style="styleOfSvg"
      version="1.1"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path fill="none" :stroke-width="lineWidth" :stroke="lineColor" :d="d"></path>
    </svg>
  </div>
</template>

<script>
export default {
  name: "TreeChartNode",
  inject: [
    "nodeKey",
    "childrenKey",
    "lineOffset",
    "lineColor",
    "lineWidth",
    "transition"
    // "collect"
  ],
  props: {
    node: {}
    // parent: {}
  },
  data() {
    const style = {
      transition: `all ${this.transition}s`
    };
    const styleOfSvg = {
      transition: `all ${this.transition}s`
    };
    return {
      style,
      styleOfSvg,
      styleOfNode: {},
      pathOfSvg: {},
      elHeight: 0,
      elWidth: 0,
      subtreeHeight: 0,
      subtreeWidth: 0
    };
  },
  computed: {
    blockHeight() {
      return Math.max(this.subtreeHeight, this.elHeight);
    },
    blockWidth() {
      return this.elWidth + this.subtreeWidth;
    },
    d() {
      const p = this.pathOfSvg;
      return `M${p.startX || 0},${p.startY || 0}C${p.midX || 0},${p.midY ||
        0},${p.midX || 0},${p.midY || 0},${p.endX || 0},${p.endY || 0}`;
    }
  },
  methods: {
    render() {
      return new Promise(resolve => {
        // 节点收集
        this._collect();
        // 先序遍历
        this._renderChildren();
        // 计算子树或自身宽高
        this._calSize();

        // 所有节点计算宽高完成后
        this.$nextTick(() => {
          // 计算位置
          this._locate();

          // 所有节点位置计算完成后
          this.$nextTick(() => {
            // 画与父节点的联线
            setTimeout(() => {
              this._drawLine();
              resolve();
            }, this.transition * 1000);
          });
        });
      });
    },
    _calSize() {
      // 该元素宽高
      this.elHeight = this.$el.getBoundingClientRect().height;
      this.elWidth = this.$el.getBoundingClientRect().width;

      // 子树宽高
      this.subtreeHeight = 0;
      this.subtreeWidth = 0;

      // 如有子树
      if (this.$refs.children && this.$refs.children.length > 0) {
        this.$refs.children.forEach(child => {
          // 高度累加
          this.subtreeHeight += child.blockHeight;
          // 宽度找最大值
          if (child.blockWidth > this.subtreeWidth) {
            this.subtreeWidth = child.blockWidth;
          }
        });
      } else {
        this.subtreeHeight = this.elHeight;
      }
    },
    _drawLine() {
      // 不存在父节点 无需画线
      if (this.$parent.$options.name !== "TreeChartNode") {
        return;
      }
      const parentRect = this.$parent.$el.getBoundingClientRect();
      const selfRect = this.$el.getBoundingClientRect();
      this.styleOfSvg = {
        height:
          Math.abs(parentRect.top - selfRect.top) + parentRect.height + "px",
        top: Math.min(0, parentRect.top - selfRect.top) + "px",
        left: parentRect.left - selfRect.left + "px",
        width: parentRect.width + selfRect.width + "px"
      };

      const startX = parentRect.width + this.lineOffset;
      const startY =
        // selfRect.top -
        // parentRect.top +
        // selfRect.height / 2 +
        // Math.max(0, parentRect.top - selfRect.top);
        selfRect.height / 2 + Math.max(0, selfRect.top - parentRect.top);

      const endX = parentRect.width - this.lineOffset;
      const endY =
        parentRect.top -
        selfRect.top +
        parentRect.height / 2 +
        Math.max(0, selfRect.top - parentRect.top);

      this.pathOfSvg = {
        startX,
        startY,
        midX: endX,
        midY: startY,
        endX,
        endY
      };
    },
    _renderChildren() {
      if (this.$refs.children && this.$refs.children.length > 0) {
        this.$refs.children.forEach(child => {
          child.render();
        });
      }
    },
    _locate() {
      // if (this.node.data.name === "new暗室逢灯") debugger;
      let top = 0;
      // 如有父节点
      if (this.$parent.$options.name === "TreeChartNode") {
        this.style.left = this.$parent.elWidth + "px";
        let upperHeight = 0;
        for (
          let index = 0;
          index < this.$parent.$refs.children.length;
          index++
        ) {
          const child = this.$parent.$refs.children[index];
          if (child === this) {
            break;
          } else {
            upperHeight += child.blockHeight;
          }
        }
        top +=
          upperHeight -
          (this.$parent.subtreeHeight - this.$parent.elHeight) / 2;
      }
      top += (this.blockHeight - this.elHeight) / 2;

      this.style.top = top + "px";
      this.$forceUpdate();
    },
    _collect() {
      // this.node[this.childrenKey] &&
      //   this.node[this.childrenKey].forEach(child => {
      //     this.collect({
      //       parent: this.node,
      //       node: child
      //     });
      //   });
    },
    _nodeKey(item, index) {
      if (this.nodeKey === undefined) {
        return index;
      } else if (typeof this.nodeKey === "function") {
        return this.nodeKey(item);
      } else {
        return item[this.nodeKey];
      }
    }
  }
};
</script>

<style scoped>
.an-treechart-node {
  position: absolute;
  width: fit-content;
}
.an-treechart-node__line {
  position: absolute;
  z-index: -1;
}
</style>

