<script>
  import '../app.css';
  import logo from '$lib/images/xene.png';
  import fingerprint from '$lib/icons/mdi-fingerprint.svg';
</script>

<nav class="flex justify-between w-full h-16 px-4 py-1 bg-gradient-to-r from-[#0e293c] via-[#141e30] to-[#0b2130]">
  <a href="/" class="self-center w-[100px] max-h-24"><img src={logo} alt="Xene Logo" /></a>
  <button class="cursor-pointer"><img src={fingerprint} alt="Login button" /></button>
</nav>

<main>
  <slot />
</main>