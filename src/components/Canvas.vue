<template>
    <div>
      <canvas id="c1" width="800" height="600"></canvas>
    </div>
  </template>
  
  <script setup>
  import { onMounted, ref } from 'vue';
  import * as fabric from 'fabric';
  
  const canvas = ref(null);
  const snapThreshold = 5; // 吸附判定阈值
  const lines = []; // 用于存储辅助线
  
  onMounted(() => {
    canvas.value = new fabric.Canvas('c1');
    const canvasWidth = canvas.value.width;
    const canvasHeight = canvas.value.height;
  
    // 添加矩形
    const rect = new fabric.Rect({
      top: 50,
      left: 50,
      width: 100,
      height: 100,
      fill: 'red',
      selectable: true,
    });
    canvas.value.add(rect);
  
    // 添加辅助线
    const createLine = (options) => {
      const line = new fabric.Line([0, 0, 0, 0], {
        ...options,
        stroke: 'blue',
        strokeWidth: 1,
        selectable: false,
        evented: false,
      });
      canvas.value.add(line);
      return line;
    };
  
    const horizontalLine = createLine({ visible: false });
    const verticalLine = createLine({ visible: false });
  
    lines.push(horizontalLine, verticalLine);
  
    // 显示辅助线
    const showLine = (line, coords) => {
      line.set({
        x1: coords.x1,
        y1: coords.y1,
        x2: coords.x2,
        y2: coords.y2,
        visible: true,
      });
      line.setCoords();
    };
  
    // 隐藏辅助线
    const hideLines = () => {
      lines.forEach((line) => line.set({ visible: false }));
    };
  
    // 吸附逻辑
    const snapTo = (value, targets) => {
      for (const target of targets) {
        if (Math.abs(value - target) <= snapThreshold) {
          return target;
        }
      }
      return value;
    };
  
    const handleObjectMoving = (e) => {
      const obj = e.target;
      const boundingBox = obj.getBoundingRect(true);
  
      const objLeft = boundingBox.left;
      const objTop = boundingBox.top;
      const objRight = boundingBox.left + boundingBox.width;
      const objBottom = boundingBox.top + boundingBox.height;
      const objCenterX = boundingBox.left + boundingBox.width / 2;
      const objCenterY = boundingBox.top + boundingBox.height / 2;
  
      // 定义参考线位置
      const canvasTargetsX = [0, canvasWidth / 2, canvasWidth];
      const canvasTargetsY = [0, canvasHeight / 2, canvasHeight];
      const objectTargetsX = [objLeft, objCenterX, objRight];
      const objectTargetsY = [objTop, objCenterY, objBottom];
  
      let snapOffsetX = 0;
      let snapOffsetY = 0;
  
      hideLines();
  
      // 水平吸附
      objectTargetsX.forEach((x) => {
        const snappedX = snapTo(x, canvasTargetsX);
        if (snappedX !== x) {
          snapOffsetX = snappedX - x;
          showLine(verticalLine, { x1: snappedX, y1: 0, x2: snappedX, y2: canvasHeight });
        }
      });
  
      // 垂直吸附
      objectTargetsY.forEach((y) => {
        const snappedY = snapTo(y, canvasTargetsY);
        if (snappedY !== y) {
          snapOffsetY = snappedY - y;
          showLine(horizontalLine, { x1: 0, y1: snappedY, x2: canvasWidth, y2: snappedY });
        }
      });
  
      // 调整对象位置
      obj.set({
        left: obj.left + snapOffsetX,
        top: obj.top + snapOffsetY,
      });
      obj.setCoords(); // 更新对象的坐标
    };
  
    // 绑定事件
    canvas.value.on('object:moving', handleObjectMoving);
  
    // 隐藏辅助线在操作结束时
    canvas.value.on('mouse:up', () => {
      hideLines();
      canvas.value.renderAll(); // 渲染画布
    });
  });
  </script>
  
  <style>
  canvas {
    border: 1px solid #ccc;
    display: block;
    margin: 20px auto;
  }
  </style>