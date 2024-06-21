<script>
    import { onMount } from 'svelte';
    // import { Pose } from '@mediapipe/pose';
    import pkg from '@mediapipe/pose';
    const { Pose } = pkg;

    // import { drawConnectors, drawLandmarks, POSE_CONNECTIONS } from '@mediapipe/drawing_utils';
    import pkgDrawing from '@mediapipe/drawing_utils';
    const { drawConnectors, drawLandmarks, POSE_CONNECTIONS } = pkgDrawing;
  
    let imageElement;
    let canvasElement;
    let canvasCtx;
  
    export let imageFile;
  
    onMount(async () => {
      imageElement = document.getElementById('uploaded_image');
      canvasElement = document.getElementById('pose_canvas');
      canvasCtx = canvasElement.getContext('2d');
  
      const pose = new Pose({
        locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/pose/${file}`
      });
  
      pose.setOptions({
        modelComplexity: 1,
        smoothLandmarks: true,
        enableSegmentation: false,
        smoothSegmentation: true,
        minDetectionConfidence: 0.5,
        minTrackingConfidence: 0.5
      });
  
      pose.onResults(onResults);
  
      if (imageFile) {
        const reader = new FileReader();
        reader.onload = async () => {
          imageElement.src = reader.result;
          await imageElement.decode(); // Ensure image is fully loaded
          pose.send({ image: imageElement });

        };
        reader.readAsDataURL(imageFile);
      }
    });
  
    function onResults(results) {
      canvasCtx.save();
      canvasCtx.clearRect(0, 0, canvasElement.width, canvasElement.height);
      canvasCtx.drawImage(results.image, 0, 0, canvasElement.width, canvasElement.height);
      if (results.poseLandmarks) {
        drawConnectors(canvasCtx, results.poseLandmarks, POSE_CONNECTIONS, { color: '#00FF00', lineWidth: 4 });
        drawLandmarks(canvasCtx, results.poseLandmarks, { color: '#FF0000', lineWidth: 2 });
      }
      canvasCtx.restore();
    }
  </script>
  
<style>
    #container {
      display: flex;
      justify-content: space-around;
    }
    #uploaded_image, #pose_canvas {
      width: 45%;
      max-width: 320px;
    }
</style>
  
<div id="container">
    <img id="uploaded_image" alt="" on:load={() => {
      const pose = new Pose({ locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/pose/${file}` });
      pose.send({ image: imageElement });
    }} />
    <canvas id="pose_canvas" width="640" height="480"></canvas>
</div>