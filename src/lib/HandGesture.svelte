<script>
    import { onMount } from 'svelte';
    // import { Hands } from '@mediapipe/hands';
    import pkg from '@mediapipe/hands';
    const { Hands } = pkg;
    // import { Camera } from '@mediapipe/camera_utils';
    import pkg2 from '@mediapipe/hands';
    const { Camera } = pkg2;
    // import { drawConnectors, drawLandmarks, HAND_CONNECTIONS } from '@mediapipe/drawing_utils';
    import pkgDrawing from '@mediapipe/drawing_utils';
    const { drawConnectors, drawLandmarks, HAND_CONNECTIONS } = pkgDrawing;
  
    
    let videoElement;
    let canvasElement;
    let canvasCtx;
  
    onMount(() => {
      videoElement = document.getElementById('video');
      canvasElement = document.getElementById('hand_canvas');
      canvasCtx = canvasElement.getContext('2d');
  
      const hands = new Hands({
        locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`
      });
  
      hands.setOptions({
        maxNumHands: 1,
        modelComplexity: 1,
        minDetectionConfidence: 0.5,
        minTrackingConfidence: 0.5
      });
  
      hands.onResults(onResults);
  
      const camera = new Camera(videoElement, {
        onFrame: async () => {
          await hands.send({ image: videoElement });
        },
        width: 640,
        height: 480
      });
      camera.start();
    });
  
    function onResults(results) {
      canvasCtx.save();
      canvasCtx.clearRect(0, 0, canvasElement.width, canvasElement.height);
      canvasCtx.drawImage(results.image, 0, 0, canvasElement.width, canvasElement.height);
      if (results.multiHandLandmarks) {
        for (const landmarks of results.multiHandLandmarks) {
          drawConnectors(canvasCtx, landmarks, HAND_CONNECTIONS, { color: '#00FF00', lineWidth: 5 });
          drawLandmarks(canvasCtx, landmarks, { color: '#FF0000', lineWidth: 2 });
        }
      }
      canvasCtx.restore();
    }
  </script>
  
  <style>
    #video, #hand_canvas {
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
    }
  </style>
  
  <video id="video" style="display:none;"></video>
  <canvas id="hand_canvas" width="640" height="480"></canvas>
  