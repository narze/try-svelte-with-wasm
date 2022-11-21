<script lang="ts">
  import svelteLogo from "./assets/svelte.svg"
  import * as photon from "@silvia-odwyer/photon"
  import { onMount } from "svelte"

  let img: HTMLImageElement
  let canvas: HTMLCanvasElement
  let resizedCanvas: HTMLCanvasElement
  let fileInput: HTMLInputElement

  function filterImage(img: HTMLImageElement) {
    // Create a canvas and get a 2D context from the canvas
    let ctx = canvas.getContext("2d")
    canvas.width = img.width
    canvas.height = img.height

    // Draw the image element onto the canvas
    ctx.clearRect(0, 0, canvas.width, canvas.height)
    ctx.drawImage(img, 0, 0)

    // Convert the ImageData found in the canvas to a PhotonImage (so that it can communicate with the core Rust library)
    let image = photon.open_image(canvas, ctx)

    let w = image.get_width() * 2
    let h = image.get_height() * 2

    // Filter the image, the PhotonImage's raw pixels are modified
    photon.filter(image, "rosetint")

    let resultCanvas = photon.resize_img_browser(
      image,
      w,
      h,
      photon.SamplingFilter.Nearest
    )
    let resultCtx = resultCanvas.getContext("2d")
    let imgdata = resultCtx.getImageData(0, 0, w, h)

    let resizedCtx = resizedCanvas.getContext("2d")
    resizedCanvas.width = imgdata.width
    resizedCanvas.height = imgdata.height

    resizedCtx.clearRect(0, 0, resizedCanvas.width, resizedCanvas.height)
    resizedCtx.putImageData(imgdata, 0, 0)

    // Place the modified image back on the canvas
    // photon.putImageData(canvas, ctx, newimage)
  }

  onMount(() => {
    fileInput.onchange = function () {
      let file = fileInput.files[0]
      let reader = new FileReader()
      reader.onload = function (e) {
        const image = new Image()
        image.src = e.target.result as string

        image.onload = () => {
          filterImage(image)
        }
      }
      reader.readAsDataURL(file)
    }
  })
</script>

<main>
  <h1>Try Svelte with Wasm</h1>

  <center>
    <p>
      Upload image to resize & filter with <a
        href="https://silvia-odwyer.github.io/photon/">Photon</a
      >, a wasm package
    </p>

    <div><input bind:this={fileInput} type="file" /></div>

    <div>Loaded image<canvas id="canvas" bind:this={canvas} /></div>
    <hr />
    <div>
      Resized+filtered image<canvas
        id="resized-canvas"
        bind:this={resizedCanvas}
      />
    </div>
  </center>
</main>

<style>
</style>
