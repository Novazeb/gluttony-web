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

  function onGlobalPointerMove(e: PointerEvent) {
    const card = (e.target as HTMLElement)?.closest?.('.key-card') as HTMLElement | null;
    if (card) {
      const rect = card.getBoundingClientRect();
      card.style.setProperty('--card-mouse-x', `${e.clientX - rect.left}px`);
      card.style.setProperty('--card-mouse-y', `${e.clientY - rect.top}px`);
    }
  }

  onMount(() => {
    window.addEventListener('pointermove', onGlobalPointerMove, { passive: true });

    let observer: IntersectionObserver | null = null;
    if (typeof IntersectionObserver !== 'undefined') {
      observer = new IntersectionObserver(
        (entries) => {
          entries.forEach((entry) => {
            if (entry.isIntersecting) {
              entry.target.classList.add('is-revealed');
              observer?.unobserve(entry.target);
            }
          });
        },
        { threshold: 0.04, rootMargin: '120px 0px 40px 0px' }
      );

      document.querySelectorAll('.reveal-on-scroll').forEach((el) => {
        observer?.observe(el);
      });
    } else {
      document.querySelectorAll('.reveal-on-scroll').forEach((el) => {
        el.classList.add('is-revealed');
      });
    }

    return () => {
      window.removeEventListener('pointermove', onGlobalPointerMove);
      observer?.disconnect();
    };
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
    position: relative;
    min-height: 100vh;
    background-color: var(--color-void-black);
    color: var(--color-pure-white);
    display: flex;
    flex-direction: column;
    overflow-x: hidden;
  }

  main {
    position: relative;
    z-index: 1;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    gap: 48px;
  }
</style>
