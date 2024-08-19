<script>
  import { is_loading } from '$lib';
  const imports = {
    lottie_component: () => import('$lib/components/Lottie.svelte'),
    lottie_module: () => import('$lib/assets/jsons/lottie/loader.json')
  };
</script>

{#if $is_loading}
  <div class="fixed inset-0 flex items-center justify-center bg-[#121212]/[0.98] z-[400]">
    {#await imports['lottie_component']() then Lottie}
      {#await imports['lottie_module']() then data}
        <svelte:component this={Lottie.default} height="80vh" speed="2" animationData={data.default} />
      {/await}
    {/await}
  </div>
{/if}