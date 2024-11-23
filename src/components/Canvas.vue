<template>
    <div>
      <canvas id="c1" width="800" height="800"></canvas>
    </div>
  </template>
  
  <script setup>
  import { onMounted } from 'vue';
  import * as fabric from 'fabric';
  
  onMounted(() => {
    const canvas = new fabric.Canvas('c1');
    const snapThreshold = 10;
  
    // 创建辅助线
    const createLine = (options) =>
      new fabric.Line([0, 0, 0, 0], {
        stroke: 'blue',
        strokeWidth: 1,
        selectable: false,
        evented: false,
        visible: false,
        ...options,
      });
  
    const horizontalLine = createLine();
    const verticalLine = createLine();
  
    canvas.add(horizontalLine, verticalLine);
  
    // 吸附计算函数
    const calculateSnap = (value, targets) => {
      for (const target of targets) {
        if (Math.abs(value - target) < snapThreshold) {
          return target;
        }
      }
      return null;
    };
  
    // 拖动事件处理
    canvas.on('object:moving', (e) => {
      const obj = e.target;
      const boundingBox = obj.getBoundingRect();
      const centerX = boundingBox.left + boundingBox.width / 2;
      const centerY = boundingBox.top + boundingBox.height / 2;
  
      const snapX = calculateSnap(centerX, [0, canvas.width / 2, canvas.width]);
      const snapY = calculateSnap(centerY, [0, canvas.height / 2, canvas.height]);
  
      // 显示或隐藏辅助线
      if (snapX !== null) {
        verticalLine.set({
          x1: snapX,
          y1: 0,
          x2: snapX,
          y2: canvas.height,
          visible: true,
        });
      } else {
        verticalLine.set({ visible: false });
      }
  
      if (snapY !== null) {
        horizontalLine.set({
          x1: 0,
          y1: snapY,
          x2: canvas.width,
          y2: snapY,
          visible: true,
        });
      } else {
        horizontalLine.set({ visible: false });
      }
  
      canvas.renderAll();
    });
  
    // 拖动结束时隐藏辅助线
    canvas.on('object:moved', () => {
      verticalLine.set({ visible: false });
      horizontalLine.set({ visible: false });
      canvas.renderAll();
    });
  
    // 添加矩形
    const rect = new fabric.Rect({
      width: 200,
      height: 200,
      fill: 'pink',
      top: 50,
      left: 50,
    });
  
    canvas.add(rect);
  });
  </script>
  
  <style>
  canvas {
    border: 1px solid #ccc;
    display: block;
  }
  </style>