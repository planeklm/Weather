<script>
  import Icon from "@iconify/svelte";
</script>

<div class="h-screen w-full flex items-center justify-center">
  <div
    class="w-[450px] h-[300px] outline-[#252525] rounded-3xl outline outline-1 bg-[#111111]"
  >
    <Icon
      icon="iconamoon:upload-thin"
      class="text-[#ffffff] text-8xl pt-[20px] mx-auto block"
    />
    <h1 class="text-[#ffffff] text-[17.5px] font-sans text-center pt-[10px]">
      Drag and drop video file to compress
    </h1>
    <h1
      class="text-[#999999] text-[15px] font-sans text-center pt-[10px] pl-[35px] pr-[35px]"
    >
      Compression happens on your device, no data is sent to our servers
    </h1>
    <button
      type="submit"
      class="flex w-[100px] mx-auto mt-5 justify-center rounded-md bg-white px-3 py-2 text-[15px] font-medium leading-6 text-[#181825] shadow-sm hover:bg-gray-100 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
      >Select File</button
    >
  </div>
</div>

<style lang="postcss">
  :global(html) {
    background-color: #070707;
  }
</style>
