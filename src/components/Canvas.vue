<template>
    <div class="canvas-container">
      <canvas id="c1" :width="canvasSize.width" :height="canvasSize.height"></canvas>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import * as fabric from 'fabric';

  const canvasSize = ref({ width: 800, height: 800 });

  let fabricCanvas = null;

  
  onMounted(() => {
    fabricCanvas = new fabric.Canvas('c1');

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
  
    fabricCanvas.add(horizontalLine, verticalLine);
  
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
    fabricCanvas.on('object:moving', (e) => {
      const obj = e.target;
      const boundingBox = obj.getBoundingRect();
      const centerX = boundingBox.left + boundingBox.width / 2;
      const centerY = boundingBox.top + boundingBox.height / 2;
  
      const snapX = calculateSnap(centerX, [0, fabricCanvas.width / 2, fabricCanvas.width]);
      const snapY = calculateSnap(centerY, [0, fabricCanvas.height / 2, fabricCanvas.height]);
  
      // 显示或隐藏辅助线
      if (snapX !== null) {
        verticalLine.set({
          x1: snapX,
          y1: 0,
          x2: snapX,
          y2: fabricCanvas.height,
          visible: true,
        });
      } else {
        verticalLine.set({ visible: false });
      }
  
      if (snapY !== null) {
        horizontalLine.set({
          x1: 0,
          y1: snapY,
          x2: fabricCanvas.width,
          y2: snapY,
          visible: true,
        });
      } else {
        horizontalLine.set({ visible: false });
      }
  
      fabricCanvas.renderAll();
    });
  
    // 拖动结束时隐藏辅助线
    fabricCanvas.on('object:moved', () => {
      verticalLine.set({ visible: false });
      horizontalLine.set({ visible: false });
      fabricCanvas.renderAll();
    });
  
  
    // 居中的手指图标的 SVG 路径
    const svgPath = 'M360.127502 290.791554c70.19549-56.336051 97.657711-58.774286 170.291435-17.067642 67.372271-57.362676 109.078915-55.181098 166.826576 8.597985 89.829695-40.295034 121.526744-25.665627 149.630605 68.912209 25.665627 85.97985 51.331254 171.959701 75.7136 258.324535 57.619333 205.325016-53.127848 377.926358-264.099302 408.853439-118.190212 17.324298-228.552409-4.619813-326.210119-80.076757-115.495322-88.803069-149.373949-222.26433-89.829695-355.468934a83.541616 83.541616 0 0 0 0-57.491004C194.45588 396.277281 143.252954 268.462459 95.258232 139.364355 64.716135 57.234348 112.839186-7.828016 195.354177 0.769969a86.236507 86.236507 0 0 1 78.40849 60.57088c27.718877 76.483568 57.234348 152.325496 86.364835 229.450705z m-90.984647 307.089227c-33.7503 75.7136-35.546893 136.027823-7.828017 202.501797 72.24874 173.37131 311.580712 243.823457 485.337007 162.078435 143.342527-67.757255 192.492203-179.659389 150.785559-331.343245-27.462221-98.812664-57.491004-196.855359-85.851523-295.15471-9.111298-31.953706-25.665627-54.026145-61.725833-43.118254s-30.413768 39.781722-24.510673 67.885584c3.208203 14.501079 3.849844 29.643799 5.774766 44.401534l-17.837611 4.363157c-11.934517-29.900455-23.612377-59.929239-35.675222-89.829695-13.987767-34.776925-31.825377-65.319021-76.996881-54.539457s-49.791316 43.888222-45.043175 82.514991a149.630605 149.630605 0 0 1-2.05325 28.103861 57.234348 57.234348 0 0 1-28.617174-33.750299c-18.735908-54.282801-52.742864-77.638522-94.192851-63.522427s-53.512832 50.047973-36.701847 103.817461c5.389782 16.810986 12.832814 33.108659 16.810986 50.047973a116.136962 116.136962 0 0 1 0 28.360518l-12.832814 3.464859a523.450463 523.450463 0 0 1-23.227392-47.48141c-41.834972-110.875509-81.360038-222.777642-125.504916-332.754854-8.726313-21.559127-32.33869-47.224754-53.256176-51.331254C133.371688 18.60758 98.851419 68.14224 123.233765 131.664667c68.912209 179.659389 138.97937 358.548809 207.891579 537.951542a382.931155 382.931155 0 0 1 12.191173 52.999519l-17.965939 5.51811z'
    const svgElement = new fabric.Path(svgPath, {
        left: 200,
        top: 200,
        fill: '#A0A0A0',
        stroke: 'none',
        strokeWidth: 1,
        scaleX: 0.3,
        scaleY: 0.3,
    });

    fabricCanvas.add(svgElement);

    // 居中路径
    fabricCanvas.centerObject(svgElement);

    // 设置路径的坐标并刷新画布
    svgElement.setCoords();
    fabricCanvas.renderAll();


  });

  </script>
  
<style scoped>
    body {
        margin: 0;
        overflow: hidden;
    }
    canvas {
        display: block;
        background-color: aqua;
    }
</style>