<script lang="ts">
  import Navbar from './components/Navbar.svelte';
  import Hero from './components/Hero.svelte';
  import CommandCenter from './components/CommandCenter.svelte';
  import SkillsGrid from './components/SkillsGrid.svelte';
  import BenchmarkSection from './components/BenchmarkSection.svelte';
  import ContrastWidget from './components/ContrastWidget.svelte';
  import PlatformGrid from './components/PlatformGrid.svelte';
  import Footer from './components/Footer.svelte';
</script>

<div class="site-root">
  <Navbar />
  <main>
    <Hero />
    <CommandCenter />
    <SkillsGrid />
    <BenchmarkSection />
    <ContrastWidget />
    <PlatformGrid />
  </main>
  <Footer />
</div>

<style>
  .site-root {
    min-height: 100vh;
    background-color: var(--color-void-black);
    color: var(--color-pure-white);
    display: flex;
    flex-direction: column;
  }

  main {
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    gap: 40px;
  }
</style>
