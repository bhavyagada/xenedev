<script>
  import Lottie from '$lib/components/Lottie.svelte';
  import loader from '$lib/assets/jsons/lottie/loader.json';
  import { is_loading } from '$lib';
</script>

{#if $is_loading}
  <div class="fixed inset-0 flex items-center justify-center bg-[#121212]/[0.98] z-[400]">
    <Lottie animationData={loader} height="80vh" speed={2} />
  </div>
{/if}