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
  
    // 添加一个简单的矩形
    canvas.add(
      new fabric.Rect({
        width: 200,
        height: 200,
        fill: 'pink',
        top: 50,
        left: 50,
      })
    );
  });
  </script>
  
  <style>
  canvas {
    border: 1px solid #ccc;
    display: block;
  }
  </style>