<script>
  import { onMount } from 'svelte';
  import * as THREE from 'three';
  import pkg from 'perlin';
  const { noise } = pkg;
  import { is_loading } from '$lib';
  import OverlayLoader from '$lib/components/OverlayLoader.svelte';

  onMount(() => {
    $is_loading = true;
    let renderer = new THREE.WebGLRenderer({ canvas: document.querySelector('canvas'), antialias: true, alpha: true });
    renderer.setClearColor(0xffffff, 0);
    renderer.setPixelRatio(window.devicePixelRatio > 1 ? 1 : 1);
    renderer.setSize(window.innerWidth, window.innerHeight);
    let scene = new THREE.Scene();
    let camera = new THREE.PerspectiveCamera(15, window.innerWidth / window.innerHeight, 1, 1000);
    camera.position.z = 60;
    let length = 30;
    let mouseJump = { x: 0, y: 0 };
    let offset = 0;

    class Spline {
      constructor() {
        const points = [];
        const colors = [];
        this.color = Math.floor(Math.random() * 80 + 180);
        for (let j = 0; j < 180; j++) {
          points.push((j / 180) * length * 2 - length, 0, 0);
          const color = new THREE.Color(`hsl(${j * 0.6 + this.color}, 70%, 70%)`);
          colors.push(color.r, color.g, color.b);
        }
        this.geometry = new THREE.BufferGeometry();
        this.geometry.setAttribute('position', new THREE.Float32BufferAttribute(points, 3));
        this.geometry.setAttribute('color', new THREE.Float32BufferAttribute(colors, 3));
        
        this.material = new THREE.LineBasicMaterial({ vertexColors: true });
        this.mesh = new THREE.Line(this.geometry, this.material);
        this.speed = (Math.random() + 0.1) * 0.0002;
        scene.add(this.mesh);
      }
    }

    let isMouseDown = false;
    let prevA = 0;
    function render(a) {
      requestAnimationFrame(render);
      for (let i = 0; i < splines.length; i++) {
        const positions = splines[i].geometry.attributes.position.array;
        for (let j = 0; j < positions.length; j += 3) {
          const x = positions[j];
          positions[j + 1] = noise.simplex2(j * 0.05 / 3 + i - offset, a * splines[i].speed) * 8;
          positions[j + 2] = noise.simplex2(x * 0.05 + i, a * splines[i].speed) * 8;

          positions[j + 1] *= 1 - Math.abs(x / length);
          positions[j + 2] *= 1 - Math.abs(x / length);
        }
        splines[i].geometry.attributes.position.needsUpdate = true;
      }
      scene.rotation.x = a * 0.0003;
      if (isMouseDown) {
        mouseJump.x += 0.001;
        if (a - prevA > 100) {
          updateColor();
          prevA = a;
        }
      } else {
        mouseJump.x -= 0.001;
      }
      mouseJump.x = Math.max(0, Math.min(0.07, mouseJump.x));
      offset += mouseJump.x;
      renderer.render(scene, camera);
    }

    let splines = [];
    for (let i = 0; i < 12; i++) splines.push(new Spline());

    function onResize() {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    }

    function updateColor() {
      for (let i = 0; i < splines.length; i++) {
        const colors = [];
        let color = Math.abs((splines[i].color - offset * 10) % 360);
        for (let j = 0; j < 180; j++) {
          const c = new THREE.Color(`hsl(${j * 0.6 + color}, 70%, 70%)`);
          colors.push(c.r, c.g, c.b);
        }
        splines[i].geometry.setAttribute('color', new THREE.Float32BufferAttribute(colors, 3));
      }
    }

    function onMouseDown(e) {
      isMouseDown = true;
      return false;
    }

    function onMouseUp() {
      isMouseDown = false;
    }

    window.addEventListener('resize', onResize);
    window.addEventListener('keydown', onMouseDown);
    document.body.addEventListener('mousedown', onMouseDown);
    document.body.addEventListener('mouseup', onMouseUp);
    document.body.addEventListener('touchstart', onMouseDown);
    document.body.addEventListener('touchend', onMouseUp);
    requestAnimationFrame(render);
    setTimeout(() => $is_loading = false, 1500);
  });
</script>

<svelte:head>
  <title>Xene - Home</title>
</svelte:head>

<div class="m-3 overflow-hidden">
  <div class="bg-[#bbb] w-full h-5 my-0 mx-auto box-border rounded-t-md">
    <div class="inline-block relative left-1.5 h-2.5 w-2.5 border border-[#9d252b] rounded-full bg-[#ff3b47]"></div>
    <div class="inline-block relative h-2.5 w-2.5 border rounded-full left-[15px] bg-[#ffc100] border-[#9d802c]"></div>
    <div class="inline-block relative h-2.5 w-2.5 border rounded-full left-6 bg-[#00d742] border-[#049931]"></div>
  </div>
  <div class="bg-[#151515] p-2.5 w-full h-[25vh] lg:h-[30vh] xl:h-[30vh] rounded-b-md my-0 mx-auto box-border">
    <p class="line1 relative overflow-hidden w-0 font-mono text-sm md:text-[1.3rem] leading-[1.95rem] text-left text-[#6cd0c6] mb-4 whitespace-nowrap">
      $ Hello, friend. Are you ready?
      <span class="cursor1 text-white font-bold">_</span>
    </p>
    <p class="line2 relative overflow-hidden w-0 font-mono text-sm md:text-[1.3rem] leading-[1.95rem] text-left text-[#ffff00] mb-4 whitespace-nowrap">
      [?] Level up your logical abilities?
      <span class="cursor2 text-white font-bold">_</span>
    </p>
    <p class="line3 relative overflow-hidden w-0 font-mono text-sm md:text-[1.3rem] leading-[1.95rem] text-left text-[#ff0000] mb-4 whitespace-nowrap">
      Buckle up to start.
      <span class="cursor3 text-white font-bold">_</span>
    </p>
    <p class="line4 relative overflow-hidden w-0 font-mono text-sm md:text-[1.3rem] leading-[1.95rem] text-left text-[#9cd9f0] mb-4 whitespace-nowrap">
      > Boot up a radical programming platform
      <span class="cursor4 text-white font-bold">_</span>
    </p>
  </div>
  <canvas class="relative top-0 left-0 cursor-pointer w-full !h-[35vh]"></canvas>
  <div class="btn">
    <span class="font-bold text-[#1de9b6] hover:text-[#121212]">Let's Begin</span>
    <div class="dot !block"></div>
  </div>
  <svelte:component this={OverlayLoader} />
</div>

<style>
  :root {
    --btn-w: 10em;
    --dot-w: calc(var(--btn-w) * 0.2);
    --tr-X: calc(var(--btn-w) - var(--dot-w));
    background-color: #121212;
  }

  .btn {
    position: relative;
    margin: 0 auto;
    cursor: pointer;
    width: 10em;
    color: #1de9b6;
    border: 0.15em solid #1de9b6;
    border-radius: 5em;
    text-align: center;
    font-size: 1.3em;
    line-height: 2em;
    font-family: Consolas, monospace;
    user-select: none;
  }

  .btn:hover,
  .btn:focus,
  .btn:active {
    background-color: #1de9b6;
    color: white;
    transition: all 0.5s ease;
    border-color: #1de9b6;
  }

  .dot {
    content: '';
    position: absolute;
    top: 0;
    width: var(--dot-w);
    height: 100%;
    border-radius: 100%;
    transition: all 300ms ease;
    display: none;
  }

  .dot:after {
    content: '';
    position: absolute;
    left: calc(50% - 0.4em);
    top: -0.4em;
    height: 0.8em;
    width: 0.8em;
    background: #1de9b6;
    border-radius: 1em;
    border: 0.25em solid #fff;
    box-shadow: 0 0 0.7em #fff, 0 0 2em #1de9b6;
  }

  .btn .dot {
    animation: atom 2s infinite linear;
  }

  @keyframes atom {
    0% { transform: translateX(0) rotate(0); }
    30% { transform: translateX(var(--tr-X)) rotate(0); }
    50% { transform: translateX(var(--tr-X)) rotate(180deg); }
    80% { transform: translateX(0) rotate(180deg); }
    100% { transform: translateX(0) rotate(360deg); }
  }

  .line1 { animation: type 2s 2s steps(50, end) forwards; }
  .line2 { animation: type 2s 4s steps(50, end) forwards; }
  .line3 { animation: type 2s 6s steps(50, end) forwards; }
  .line4 { animation: type 2s 8s steps(50, end) forwards; }

  .cursor1 { animation: blink 1s 2s 2 forwards; }
  .cursor2 { animation: blink 1s 4s 2 forwards; }
  .cursor3 { animation: blink 1s 6s 2 forwards; }
  .cursor4 { animation: blink 1s 8s infinite; }

  @keyframes blink {
    0%, 40%, 100% { opacity: 0; }
    50%, 90% { opacity: 1; }
  }

  @keyframes type {
    to { width: 100%; }
  }
</style>