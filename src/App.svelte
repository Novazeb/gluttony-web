<script lang="ts">
  import { onMount } from 'svelte';
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

  onMount(() => {
    if (typeof IntersectionObserver !== 'undefined') {
      const observer = new IntersectionObserver(
        (entries) => {
          entries.forEach((entry) => {
            if (entry.isIntersecting) {
              entry.target.classList.add('is-revealed');
              observer.unobserve(entry.target);
            }
          });
        },
        { threshold: 0.08, rootMargin: '0px 0px -40px 0px' }
      );

      document.querySelectorAll('.reveal-on-scroll').forEach((el) => {
        observer.observe(el);
      });

      return () => observer.disconnect();
    } else {
      document.querySelectorAll('.reveal-on-scroll').forEach((el) => {
        el.classList.add('is-revealed');
      });
    }
  });
</script>

<div class="site-root">
  <Navbar />
  <main>
    <Hero />
    <div class="reveal-on-scroll"><CommandCenter /></div>
    <div class="reveal-on-scroll"><DiffViewer /></div>
    <div class="reveal-on-scroll"><RuleCheatSheet /></div>
    <div class="reveal-on-scroll"><SkillsGrid /></div>
    <div class="reveal-on-scroll"><SetupWizard /></div>
    <div class="reveal-on-scroll"><BenchmarkSection /></div>
    <div class="reveal-on-scroll"><ContrastWidget /></div>
    <div class="reveal-on-scroll"><PlatformGrid /></div>
    <div class="reveal-on-scroll"><FaqSection /></div>
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
