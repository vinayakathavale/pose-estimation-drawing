<script>
  import { onMount } from 'svelte';
  import { PoseLandmarker, FilesetResolver, DrawingUtils } from '@mediapipe/tasks-vision';

  let videoElement;
  let canvasElement;
  let canvasCtx;
  let poseLandmarker;
  let webcamRunning = false;

  onMount(async () => {
    const vision = await FilesetResolver.forVisionTasks(
      "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@0.10.0/wasm"
    );
    poseLandmarker = await PoseLandmarker.createFromOptions(vision, {
      baseOptions: {
        modelAssetPath: `https://storage.googleapis.com/mediapipe-models/pose_landmarker/pose_landmarker_lite/float16/1/pose_landmarker_lite.task`,
        delegate: "GPU"
      },
      runningMode: "VIDEO",
      numPoses: 2
    });

    videoElement = document.getElementById('webcam');
    canvasElement = document.getElementById('output_canvas');
    canvasCtx = canvasElement.getContext('2d');

    if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
      const stream = await navigator.mediaDevices.getUserMedia({ video: true });
      videoElement.srcObject = stream;
      videoElement.play();
      predictWebcam();
    }
  });

  async function predictWebcam() {
    if (webcamRunning) {
      let startTimeMs = performance.now();
      poseLandmarker.detectForVideo(videoElement, startTimeMs, (result) => {
        canvasCtx.save();
        canvasCtx.clearRect(0, 0, canvasElement.width, canvasElement.height);
        const drawingUtils = new DrawingUtils(canvasCtx);
        for (const landmark of result.landmarks) {
          drawingUtils.drawLandmarks(landmark, {
            radius: (data) => DrawingUtils.lerp(data.from?.z ?? 0, -0.15, 0.1, 5, 1)
          });
          drawingUtils.drawConnectors(landmark, PoseLandmarker.POSE_CONNECTIONS);
        }
        canvasCtx.restore();
      });
      window.requestAnimationFrame(predictWebcam);
    }
  }

  function toggleWebcam() {
    webcamRunning = !webcamRunning;
    if (webcamRunning) {
      predictWebcam();
    }
  }
</script>

<style>
  .video-container {
    position: relative;
    width: 640px; /* Adjust as needed */
    height: 480px; /* Adjust as needed */
  }
  #webcam, #output_canvas {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
</style>

<div class="video-container">
  <video id="webcam" autoplay playsinline aria-hidden="true"></video>
  <canvas id="output_canvas"></canvas>
</div>
<button on:click={toggleWebcam}>Toggle Webcam</button>