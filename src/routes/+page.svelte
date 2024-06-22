<script>
    import { onMount } from 'svelte';
    import PoseEstimation from '$lib/PoseEstimation.svelte';
  
    let showModal = false;

    function toggleModal() {
    showModal = !showModal;
    }
    function handleKeyDown(event) {
        if (event.key === 'Enter') {
            toggleModal();
        }
    }

</script>
  
  <main>
    <h2>Pose Estimation App</h2>
    <div class="top-right">
      <button on:click={toggleModal}>Usage Instructions</button>
      {#if showModal}
      <div class="modal">

        <h2>How to Use:</h2>
        <button on:keydown={handleKeyDown}>
            <ol>
              <li>Give the website permission to use the device's camera</li>
              <li>Click on the button "Toggle Pose Estimation" to get the pose</li>
              <li>Click on the button again to freeze the output of the pose model. Now you can use this to come up with the initial version of your sketch</li>
                   <!-- ... rest of the list items ... -->
            </ol>
        </button>
      </div>
      {/if}
    </div>
    <div>
          <PoseEstimation />
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
  
    .top-right {
        position: absolute;
        top: 5%;
        right: 5%;
    }

    .modal {
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background-color: white;
        padding: 20px;
        border-radius: 8px;
        z-index: 10;
    }


    div {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
  </style>
  