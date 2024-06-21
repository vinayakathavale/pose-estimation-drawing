<script>
    import { onMount } from 'svelte';
    import HandGesture from '$lib/HandGesture.svelte';
    import PoseEstimation from '$lib/PoseEstimation.svelte';
  
    let imageFile = null;
  
    function handleFileUpload(event) {
      imageFile = event.target.files[0];
    }
  </script>
  
  <main>
    <h1>Hand Gesture & Pose Estimation</h1>
    <input type="file" accept="image/*" on:change={handleFileUpload} />
    {#if imageFile}
      <div style="display: flex; gap: 20px;">
        <div>
          <h2>Pose Estimation</h2>
          <PoseEstimation {imageFile} />
        </div>
      </div>
    {/if}
  </main>
  
  <style>
    main {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      background-color: #282c34;
      color: white;
    }
  
    input[type="file"] {
      margin-bottom: 20px;
    }
  
    div {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
  </style>
  