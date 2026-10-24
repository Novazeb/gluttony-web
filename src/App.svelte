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
        { threshold: 0.08, rootMargin: '0px 0px -40px 0px' }
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
  <!-- Cosmic Aurora Atmosphere Layer -->
  <div class="global-aurora-layer" aria-hidden="true">
    <div class="aurora-orb orb-1"></div>
    <div class="aurora-orb orb-2"></div>
    <div class="aurora-orb orb-3"></div>
  </div>

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

  /* Cosmic Aurora Atmosphere */
  .global-aurora-layer {
    position: fixed;
    inset: 0;
    pointer-events: none;
    z-index: 0;
    overflow: hidden;
  }

  .aurora-orb {
    position: absolute;
    border-radius: 50%;
    filter: blur(140px);
    opacity: 0.14;
    will-change: transform;
  }

  .orb-1 {
    top: 15%;
    left: -12%;
    width: 650px;
    height: 650px;
    background: radial-gradient(circle, #ff6363 0%, rgba(255, 99, 99, 0) 70%);
    animation: floatOrb1 26s ease-in-out infinite alternate;
  }

  .orb-2 {
    top: 48%;
    right: -12%;
    width: 700px;
    height: 700px;
    background: radial-gradient(circle, #2458f5 0%, rgba(36, 88, 245, 0) 70%);
    animation: floatOrb2 30s ease-in-out infinite alternate;
  }

  .orb-3 {
    bottom: 8%;
    left: 20%;
    width: 550px;
    height: 550px;
    background: radial-gradient(circle, #ff6363 0%, rgba(255, 99, 99, 0) 70%);
    animation: floatOrb3 24s ease-in-out infinite alternate;
  }

  @keyframes floatOrb1 {
    0% { transform: translate(0, 0) scale(1); }
    100% { transform: translate(90px, 110px) scale(1.15); }
  }

  @keyframes floatOrb2 {
    0% { transform: translate(0, 0) scale(1); }
    100% { transform: translate(-110px, -90px) scale(1.12); }
  }

  @keyframes floatOrb3 {
    0% { transform: translate(0, 0) scale(1); }
    100% { transform: translate(70px, -70px) scale(1.16); }
  }

  main {
    position: relative;
    z-index: 1;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    gap: 48px;
  }

  @media (prefers-reduced-motion: reduce) {
    .aurora-orb {
      animation: none !important;
      opacity: 0.08 !important;
      transform: none !important;
    }
  }
</style>
