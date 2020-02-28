# vue-treecharts

[![npm](https://img.shields.io/badge/npm-vue--treecharts-brightgreen.svg)](https://www.npmjs.com/package/vue-treecharts)

> Tree chart component based on Vue 2.0

## Introduction

高度可定制的树图表组件组件，支持 vue slot 特性自定义树节点 DOM，实现动态可编辑的树图表，如**决策树**。

实现思路：树节点（TreeChartNode）组件基于递归实现，先序遍历以自上向下（DOM）计算子树高度，自底向顶（树结构）计算子树宽度。随后用 svg path d 计算并绘制贝塞尔曲线以连接父子节点

Contact me for any questions: nerfthisan@163.com

## Install

`npm install vue-treecharts --save`

## Example

<img src="https://raw.githubusercontent.com/i58000/vue-treecharts/master/example.png" width="460"/>

```html
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
        <div :style="{ background: node.data.bgcolor }">
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
  import TreeChart from 'vue-treecharts'
  export default {
    components: {
      TreeChart
    },
    data() {
      return {
        treedata: {
          data: { name: 'root', bgcolor: '#1890ff' },
          children: [
            {
              data: { name: 'L1#1', bgcolor: '#1890ff' },
              children: [
                {
                  data: { name: 'L2#1 - ANJINSHUO', bgcolor: '#1890ff' }
                },
                {
                  data: {
                    name: 'L2#2 - nerfthisan@163.com',
                    bgcolor: '#f5222d'
                  }
                },
                {
                  data: { name: 'L2#3 - i58000 @ github', bgcolor: '#52c41a' }
                },
                {
                  data: { name: 'L2#4 - anjs @ npm', bgcolor: '#faad14' }
                }
              ]
            },
            {
              data: {
                name: 'L1#2-loooooooooooooong loooooooooooooong ago',
                bgcolor: '#1890ff'
              }
            },
            {
              data: {
                name: 'L1#3',
                bgcolor: '#1890ff',
                editable: true
              },
              children: [
                {
                  data: { name: 'children 1', bgcolor: '#1890ff' }
                },
                {
                  data: { name: 'children 2', bgcolor: '#1890ff' }
                },
                {
                  data: { name: 'children 3', bgcolor: '#1890ff' }
                }
              ]
            }
          ]
        }
      }
    }
  }
</script>
```

## Document

#### props

- data _数据源_

  > type: **Object**

- nodeKey _子节点数组中元素的唯一 key（如 ID）_

  > type: **String | Number | Function | Boolean**
  >
  > default: **undefined**
  >
  > 若为静态数据可无需设置该值，若需支持数据节点的增删改则必须为数组中每个元素设置唯一 key 值，否则可能导致 dom 更新错误，参考 [Vue key](https://cn.vuejs.org/v2/api/#key)

- childrenKey _子节点数组所在的属性键名_

  > type: **String**
  >
  > default: **"children"**

- transition _节点增删改动画时间_

  > type: **Number**
  >
  > default: **0**

- lineColor _父子节点连线颜色_

  > type: **String**
  >
  > default: **"#666"**

- lineOffset _父子节点连线偏移量_

  > type: **Number**
  >
  > default: **20**

- lineWidth _父子节点连线宽度_

  > type: **Number**
  >
  > default: 2

#### slots

- default _节点自定义内容_

  > slotProps: { node, childrenVisible }
  >
  > - node: 数据节点对象
  >
  > - childrenVisible: Function，控制当前节点的子节点是否显示的函数，参数必须为 `boolean` 类型，返回值为子节点是否显示。
