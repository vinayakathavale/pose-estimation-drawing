<script>
    import { onMount } from 'svelte';
    import PoseEstimation from '$lib/PoseEstimation.svelte';
  
    let imageFile = null;
  
    function handleFileUpload(event) {
      imageFile = event.target.files[0];
    }
  </script>
  
  <main>
    <h2>Pose Estimation App</h2>
    <div>
          <PoseEstimation {imageFile} />
    <div />
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
  
  
    div {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
  </style>
  