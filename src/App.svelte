<script lang="ts">
  import Navbar from './components/Navbar.svelte';
  import Hero from './components/Hero.svelte';
  import CommandCenter from './components/CommandCenter.svelte';
  import DiffViewer from './components/DiffViewer.svelte';
  import RuleCheatSheet from './components/RuleCheatSheet.svelte';
  import SkillsGrid from './components/SkillsGrid.svelte';
  import SetupWizard from './components/SetupWizard.svelte';
  import BenchmarkSection from './components/BenchmarkSection.svelte';
  import ContrastWidget from './components/ContrastWidget.svelte';
  import PlatformGrid from './components/PlatformGrid.svelte';
  import FaqSection from './components/FaqSection.svelte';
  import Footer from './components/Footer.svelte';
</script>

<div class="site-root">
  <Navbar />
  <main>
    <Hero />
    <CommandCenter />
    <DiffViewer />
    <RuleCheatSheet />
    <SkillsGrid />
    <SetupWizard />
    <BenchmarkSection />
    <ContrastWidget />
    <PlatformGrid />
    <FaqSection />
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
    gap: 48px;
  }
</style>
