<script lang="ts">
  import svelteLogo from "./assets/svelte.svg";
  import Counter from "./lib/Counter.svelte";

  let img;
  let canvas;
  let photon;

  import("@silvia-odwyer/photon").then((importedPhoton) => {
    photon = importedPhoton;
  });

  function filterImage() {
    // Create a canvas and get a 2D context from the canvas
    let ctx = canvas.getContext("2d");

    // Draw the image element onto the canvas
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.drawImage(img, 100 * Math.random(), 0);

    // Convert the ImageData found in the canvas to a PhotonImage (so that it can communicate with the core Rust library)
    let image = photon.open_image(canvas, ctx);

    // Filter the image, the PhotonImage's raw pixels are modified
    photon.filter(image, "radio");

    // Place the modified image back on the canvas
    photon.putImageData(canvas, ctx, image);
  }
</script>

<main>
  <div>
    <a href="https://vitejs.dev" target="_blank" rel="noreferrer">
      <img src="/vite.svg" class="logo" alt="Vite Logo" />
    </a>
    <a href="https://svelte.dev" target="_blank" rel="noreferrer">
      <img
        bind:this={img}
        src={svelteLogo}
        class="logo svelte"
        alt="Svelte Logo"
      />
    </a>
  </div>
  <h1>Vite + Svelte</h1>

  <div class="card">
    <button on:click={filterImage}>Click</button>
    <canvas id="canvas" bind:this={canvas} />
  </div>

  <p>
    Check out <a
      href="https://github.com/sveltejs/kit#readme"
      target="_blank"
      rel="noreferrer">SvelteKit</a
    >, the official Svelte app framework powered by Vite!
  </p>

  <p class="read-the-docs">Click on the Vite and Svelte logos to learn more</p>
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
  .read-the-docs {
    color: #888;
  }
</style>
