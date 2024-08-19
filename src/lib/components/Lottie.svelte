<script>
  import { onMount, onDestroy } from 'svelte';
  import lottie from 'lottie-web';

  export let animationData;
  export let speed = 1;
  export let width = -1;
  export let height = -1;
  export let loop = true;
  export let autoPlay = true;

  let lottieContainer;
  let anim;

  onMount(() => {
    const style = {
      width: width != -1 ? `${width}` : '100%',
      height: height != -1 ? `${height}` : '100%',
      overflow: 'hidden',
      margin: '0 auto'
    };
    lottieContainer.style.cssText = Object.entries(style).map(([key, value]) => `${key}: ${value}`).join('; ');

    anim = lottie.loadAnimation({
      container: lottieContainer,
      renderer: 'svg',
      loop,
      autoplay: autoPlay,
      animationData,
      rendererSettings: {
        clearCanvas: true,
        progressiveLoad: true,
        hideOnTransparent: true
      }
    });
    anim.setSpeed(speed);
  });

  onDestroy(() => {
    if (anim) anim.destroy();
  });
</script>

<div bind:this={lottieContainer}></div>