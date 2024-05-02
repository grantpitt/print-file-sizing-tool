<script lang="ts">
  // let file: File | null = null;
  // let image: HTMLImageElement | null = null;
  type fileInfo = {
    image: HTMLImageElement;
    width: number;
    height: number;
  };

  let fileInfo: fileInfo | null = null;

  type FileUploadEvent = Event & {
    target: HTMLInputElement & EventTarget;
  };

  async function handleFileUpload(event: FileUploadEvent) {
    const file = event?.target?.files?.[0];
    if (!file) {
      return;
    }
    console.log(file);

    const image = await createImage(await readFileAsDataURL(file));
    // console.log(`Width: ${image.width}, Height: ${image.height}`);
    fileInfo = {
      image: image,
      width: image.width,
      height: image.height,
    };
    console.log(image);
  }

  function readFileAsDataURL(file: File): Promise<string> {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.onload = () => resolve(reader.result as string);
      reader.onerror = () => reject(reader.error);
      reader.readAsDataURL(file);
    });
  }

  function createImage(dataURL: string): Promise<HTMLImageElement> {
    return new Promise((resolve, reject) => {
      const img = new Image();
      img.onload = () => resolve(img);
      img.onerror = () => reject(new Error("Error loading image"));
      img.src = dataURL;
    });
  }

  function truncate(value: number, decimals = 1) {
    return Math.trunc(value * 10 ** decimals) / 10 ** decimals;
  }

  function roundToNearestPointFive(value: number) {
    return Math.round(value * 2) / 2;
  }
</script>

<main class="mx-auto my-8 px-4 max-w-3xl">
  <h1 class="font-medium text-3xl my-6">Fize Size Tool</h1>

  <section class="my-6">
    <label for="artwork">Upload your design file:</label>
    <input
      type="file"
      id="artwork"
      name="artwork"
      accept="image/png, image/jpeg"
      on:change={handleFileUpload}
    />
  </section>

  {#if fileInfo}
    <img src={fileInfo.image.src} alt="Uploaded file" class="max-w-96 max-h-96 my-6" />

    <h2 class="font-medium text-xl my-2">Uploaded File Dimensions (width x height)</h2>
    <p>{fileInfo.width}px x {fileInfo.height}px</p>

    <h2 class="font-medium text-xl mt-6 mb-2">Recommended Print Sizes for Uploaded File</h2>
    <table class="mb-6 w-full table-fixed">
      <thead>
        <tr>
          <th class="px-3 py-2 text-left text-sm font-medium tracking-wider border border-gray-300"
            >Standard Website Quality (72 DPI)</th
          >
          <th class="px-3 py-2 text-left text-sm font-medium tracking-wider border border-gray-300"
            >Low Quality Print (100 DPI)</th
          >
          <th class="px-3 py-2 text-left text-sm font-medium tracking-wider border border-gray-300"
            >Medium Quality Print (200 DPI)</th
          >
          <th class="px-3 py-2 text-left text-sm font-medium tracking-wider border border-gray-300"
            >High Quality Print (300 DPI)
            <span class="bg-pink-200 rounded-md px-1">*Recommended</span>
          </th>
        </tr>
      </thead>
      <tbody class="bg-white divide-y divide-gray-200">
        <tr>
          <td class="px-3 py-2 whitespace-nowrap text-sm border border-gray-300"
            >{roundToNearestPointFive(fileInfo.width / 72)}" x {roundToNearestPointFive(
              fileInfo.height / 72,
            )}"</td
          >
          <td class="px-3 py-2 whitespace-nowrap text-sm border border-gray-300"
            >{roundToNearestPointFive(fileInfo.width / 100)}" x {roundToNearestPointFive(
              fileInfo.height / 100,
            )}"</td
          >
          <td class="px-3 py-2 whitespace-nowrap text-sm border border-gray-300"
            >{roundToNearestPointFive(fileInfo.width / 200)}" x {roundToNearestPointFive(
              fileInfo.height / 200,
            )}"</td
          >
          <td class="px-3 py-2 whitespace-nowrap text-sm border border-gray-300"
            >{roundToNearestPointFive(fileInfo.width / 300)}" x {roundToNearestPointFive(
              fileInfo.height / 300,
            )}"</td
          >
        </tr>
      </tbody>
    </table>
  {/if}

  <h2 class="font-medium text-xl mt-6 mb-2">
    Recommended File Size for Standard Print Material Sizes
  </h2>

  <table class="mb-6 w-full table-fixed">
    <thead>
      <tr>
        <th class="px-3 py-2 text-left text-sm font-medium tracking-wider border border-gray-300"
          >Print Material Size</th
        >
        <th class="px-3 py-2 text-left text-sm font-medium tracking-wider border border-gray-300"
          >Recommended File Size (300 DPI)</th
        >
      </tr>
    </thead>
    <tbody class="bg-white divide-y divide-gray-200">
      {#each [{ width: 3, height: 5 }, { width: 4, height: 6 }, { width: 4, height: 9 }, { width: 5, height: 7 }, { width: 5, height: 8 }, { width: 5.5, height: 8.5 }, { width: 6, height: 9 }, { width: 8.5, height: 11 }, { width: 11, height: 17 }, { width: 12, height: 18 }, { width: 18, height: 24 }, { width: 24, height: 36 }] as { width, height }}
        <tr>
          <td class="px-3 py-2 whitespace-nowrap text-sm border border-gray-300"
            >{width}" x {height}"</td
          >
          <td class="px-3 py-2 whitespace-nowrap text-sm border border-gray-300"
            >{truncate(width * 300)}px x {truncate(height * 300)}px</td
          >
        </tr>
      {/each}
    </tbody>
  </table>
  <p>
    In order for printed materials to look crisp and clear, it is recommended to use high resolution
    images. The resolution for printed materials is measured in DPI (dots per inch). The higher the
    DPI, the better the quality of the printed image.
  </p>
</main>
