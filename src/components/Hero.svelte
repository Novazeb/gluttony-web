<script lang="ts">
  import { onMount } from 'svelte';

  let copyState = $state<'idle' | 'copied'>('idle');
  let starCount = $state<number | null>(null);
  const installCmd = '/plugin install gluttony@gluttony';

  let heroSectionEl: HTMLElement;
  let canvasEl: HTMLCanvasElement;

  function onHeroMouseMove(e: MouseEvent) {
    if (!heroSectionEl) return;
    const rect = heroSectionEl.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;
    heroSectionEl.style.setProperty('--mouse-x', `${x}px`);
    heroSectionEl.style.setProperty('--mouse-y', `${y}px`);
  }

  onMount(async () => {
    // Live GitHub stars fetch
    try {
      const res = await fetch('https://api.github.com/repos/Novazeb/gluttony');
      if (res.ok) {
        const data = await res.json();
        if (typeof data.stargazers_count === 'number') {
          starCount = data.stargazers_count;
        }
      }
    } catch {
      // Fallback
    }

    // Majestic Constellation & Stardust Canvas Animation
    if (!canvasEl) return;
    const canvas = canvasEl;
    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    let animId: number;
    let w = (canvas.width = canvas.offsetWidth);
    let h = (canvas.height = canvas.offsetHeight);

    const onResize = () => {
      if (!canvas) return;
      w = canvas.width = canvas.offsetWidth;
      h = canvas.height = canvas.offsetHeight;
    };
    window.addEventListener('resize', onResize);

    const colors = ['#ff6363', '#63a1ff', '#ffffff', '#ff9999'];
    const pCount = Math.min(42, Math.floor((w * h) / 28000));
    const particles = Array.from({ length: pCount }, () => ({
      x: Math.random() * w,
      y: Math.random() * h,
      vx: (Math.random() - 0.5) * 0.32,
      vy: (Math.random() - 0.5) * 0.32,
      radius: Math.random() * 1.6 + 0.6,
      baseAlpha: Math.random() * 0.45 + 0.25,
      alpha: Math.random() * 0.45 + 0.25,
      color: colors[Math.floor(Math.random() * colors.length)],
      phase: Math.random() * Math.PI * 2,
    }));

    const render = () => {
      ctx.clearRect(0, 0, w, h);

      // Faint luminous connection lines between nearby stardust
      for (let i = 0; i < particles.length; i++) {
        for (let j = i + 1; j < particles.length; j++) {
          const dx = particles[i].x - particles[j].x;
          const dy = particles[i].y - particles[j].y;
          const dist = Math.hypot(dx, dy);
          if (dist < 115) {
            const lineAlpha = (1 - dist / 115) * 0.14;
            ctx.strokeStyle = `rgba(255, 99, 99, ${lineAlpha})`;
            ctx.lineWidth = 0.65;
            ctx.beginPath();
            ctx.moveTo(particles[i].x, particles[i].y);
            ctx.lineTo(particles[j].x, particles[j].y);
            ctx.stroke();
          }
        }
      }

      // Draw glowing particles
      for (const p of particles) {
        p.x += p.vx;
        p.y += p.vy;
        p.phase += 0.02;
        p.alpha = p.baseAlpha + Math.sin(p.phase) * 0.22;

        if (p.x < -10) p.x = w + 10;
        if (p.x > w + 10) p.x = -10;
        if (p.y < -10) p.y = h + 10;
        if (p.y > h + 10) p.y = -10;

        ctx.fillStyle = p.color;
        ctx.globalAlpha = Math.max(0, Math.min(1, p.alpha));
        ctx.beginPath();
        ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
        ctx.fill();

        // Subtle soft radial halo for larger motes
        if (p.radius > 1.3) {
          ctx.beginPath();
          ctx.arc(p.x, p.y, p.radius * 2.5, 0, Math.PI * 2);
          ctx.fillStyle = p.color;
          ctx.globalAlpha = p.alpha * 0.16;
          ctx.fill();
        }
      }

      ctx.globalAlpha = 1;
      animId = requestAnimationFrame(render);
    };

    animId = requestAnimationFrame(render);

    return () => {
      cancelAnimationFrame(animId);
      window.removeEventListener('resize', onResize);
      if (heroSectionEl) {
        heroSectionEl.removeEventListener('mousemove', onHeroMouseMove);
      }
    };
  });

  $effect(() => {
    if (heroSectionEl) {
      heroSectionEl.addEventListener('mousemove', onHeroMouseMove, { passive: true });
    }
  });

  function copyCommand() {
    navigator.clipboard.writeText(installCmd);
    copyState = 'copied';
    setTimeout(() => {
      copyState = 'idle';
    }, 2000);
  }
</script>

<section
  bind:this={heroSectionEl}
  class="hero-section"
>
  <!-- Atmospheric backdrop: Raycast cinematic red/blue geometry + Canvas Particle Starfield -->
  <div class="hero-atmosphere" aria-hidden="true">
    <canvas bind:this={canvasEl} class="hero-canvas"></canvas>
    <div class="hero-cursor-spotlight"></div>
    <div class="glow-blue"></div>
    <div class="glow-coral-diagonal"></div>
    <div class="glow-coral-accent"></div>
    <div class="cosmic-aura-ring"></div>
    <div class="mesh-grid"></div>
  </div>

  <!-- Smooth bottom feather fade into page content (eliminates all sharp dividing lines) -->
  <div class="hero-bottom-fade" aria-hidden="true"></div>

  <div class="hero-content container">
    <!-- Eyebrow Pill -->
    <div class="eyebrow-container">
      <div class="eyebrow-pill">
        <span class="pulse-dot"></span>
        <span class="eyebrow-text font-mono">GLUTTONY v2.2.0 · AGENT PROTOCOL</span>
      </div>
    </div>

    <!-- 60px / 500 signature headline with holographic text shimmer -->
    <h1 class="hero-headline">
      Maximum signal, zero fluff.<br />
      Relentless execution.
    </h1>

    <!-- Subheadline in #9c9c9d -->
    <p class="hero-subheadline">
      High-precision lean engineering mode for autonomous coding agents.
      Enforces YAGNI, eliminates AI boilerplate, preserves contracts, and stops UI slop before it enters your codebase.
    </p>

    <!-- CTAs & Action buttons with interactive hover glow -->
    <div class="hero-actions">
      <button onclick={copyCommand} class="btn-mist hero-btn hero-btn-glow">
        <svg width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
          <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
        </svg>
        <span>{copyState === 'copied' ? 'Copied to clipboard' : 'Copy Install Command'}</span>
      </button>

      <a href="https://github.com/Novazeb/gluttony" target="_blank" rel="noopener noreferrer" class="btn-ghost hero-btn">
        <svg width="15" height="15" viewBox="0 0 24 24" fill="currentColor">
          <path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.53 1.032 1.53 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z" />
        </svg>
        <span>View on GitHub</span>
        {#if starCount !== null}
          <span class="hero-star-pill font-mono">★ {starCount}</span>
        {/if}
      </a>
    </div>

    <!-- Command strip preview -->
    <div
      class="terminal-command-well"
      onclick={copyCommand}
      role="button"
      tabindex="0"
      onkeydown={(e) => e.key === 'Enter' && copyCommand()}
    >
      <div class="cmd-content-left">
        <span class="cmd-prompt font-mono">$</span>
        <span class="cmd-text font-mono">{installCmd}</span>
      </div>
      <span class="cmd-badge font-mono">{copyState === 'copied' ? 'COPIED' : 'COPY'}</span>
    </div>

    <!-- Raycast Footer Meta Strip -->
    <div class="hero-meta-strip font-mono">
      <span>v2.2.0</span>
      <span class="divider">|</span>
      <span>YAGNI First</span>
      <span class="divider">|</span>
      <span>Preserve Contracts</span>
      <span class="divider">|</span>
      <span>Zero Slop</span>
      <span class="divider">|</span>
      <span>14+ Harnesses</span>
    </div>
  </div>
</section>

<style>
  .hero-section {
    position: relative;
    min-height: 84vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding-top: 140px;
    padding-bottom: 75px;
    overflow: hidden;
    width: 100%;
    max-width: 100vw;
  }

  /* Atmospheric backdrop with smooth bottom fade to avoid sharp cutoffs */
  .hero-atmosphere {
    position: absolute;
    inset: 0;
    pointer-events: none;
    z-index: 0;
    overflow: hidden;
    max-width: 100vw;
    mask-image: linear-gradient(to bottom, black 0%, black 72%, transparent 100%);
    -webkit-mask-image: linear-gradient(to bottom, black 0%, black 72%, transparent 100%);
  }

  /* Seamless bottom feather fade eliminating any dividing lines */
  .hero-bottom-fade {
    position: absolute;
    bottom: -1px;
    left: 0;
    right: 0;
    height: 160px;
    background: linear-gradient(to bottom, transparent, var(--color-void-black));
    pointer-events: none;
    z-index: 2;
  }

  .hero-canvas {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 1;
    opacity: 0.85;
  }

  /* Interactive Cursor Spotlight that smoothly follows mouse */
  .hero-cursor-spotlight {
    position: absolute;
    inset: 0;
    z-index: 2;
    background: radial-gradient(
      650px circle at var(--mouse-x, 50%) var(--mouse-y, 35%),
      rgba(255, 99, 99, 0.08),
      rgba(99, 161, 255, 0.04) 40%,
      transparent 70%
    );
    pointer-events: none;
    transition: opacity 0.3s ease;
  }

  .glow-blue {
    position: absolute;
    top: 20%;
    left: 50%;
    transform: translate(-50%, -40%);
    width: 850px;
    max-width: 100vw;
    height: 520px;
    background: radial-gradient(circle, rgba(4, 63, 150, 0.45) 0%, rgba(6, 18, 37, 0.18) 60%, rgba(4, 5, 6, 0) 80%);
    filter: blur(55px);
    animation: ambientGlowFloat 14s ease-in-out infinite;
  }

  .glow-coral-diagonal {
    position: absolute;
    top: 30%;
    left: 50%;
    transform: translate(-50%, -50%) rotate(-25deg);
    width: 720px;
    max-width: 120vw;
    height: 130px;
    background: linear-gradient(90deg, transparent 0%, rgba(255, 99, 99, 0.35) 40%, rgba(255, 99, 99, 0.16) 75%, transparent 100%);
    filter: blur(50px);
    animation: ambientDiagonalShift 10s ease-in-out infinite;
  }

  .glow-coral-accent {
    position: absolute;
    top: 38%;
    left: 50%;
    transform: translateX(-50%);
    width: 320px;
    max-width: 80vw;
    height: 100px;
    background: rgba(255, 99, 99, 0.25);
    filter: blur(65px);
    animation: ambientPulse 7s ease-in-out infinite;
  }

  .cosmic-aura-ring {
    position: absolute;
    top: 36%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 680px;
    height: 380px;
    border-radius: 50%;
    border: 1px solid rgba(255, 99, 99, 0.08);
    box-shadow: 0 0 80px rgba(99, 161, 255, 0.06) inset;
    filter: blur(1px);
    animation: auraSpin 25s linear infinite;
  }

  @keyframes auraSpin {
    0% {
      transform: translate(-50%, -50%) rotate(0deg) scale(0.96);
    }
    50% {
      transform: translate(-50%, -50%) rotate(180deg) scale(1.04);
    }
    100% {
      transform: translate(-50%, -50%) rotate(360deg) scale(0.96);
    }
  }

  @keyframes ambientGlowFloat {
    0%, 100% {
      transform: translate(-50%, -40%) scale(1);
      opacity: 0.85;
    }
    50% {
      transform: translate(-50%, -35%) scale(1.08);
      opacity: 1;
    }
  }

  @keyframes ambientDiagonalShift {
    0%, 100% {
      transform: translate(-50%, -50%) rotate(-25deg) scale(1);
      opacity: 0.8;
    }
    50% {
      transform: translate(-48%, -48%) rotate(-21deg) scale(1.06);
      opacity: 1;
    }
  }

  @keyframes ambientPulse {
    0%, 100% {
      opacity: 0.55;
      transform: translateX(-50%) scale(0.94);
    }
    50% {
      opacity: 1;
      transform: translateX(-50%) scale(1.12);
    }
  }

  .mesh-grid {
    position: absolute;
    inset: 0;
    background-image: linear-gradient(to right, rgba(255, 255, 255, 0.02) 1px, transparent 1px),
                      linear-gradient(to bottom, rgba(255, 255, 255, 0.02) 1px, transparent 1px);
    background-size: 40px 40px;
    mask-image: radial-gradient(circle at 50% 40%, black 25%, transparent 75%);
    -webkit-mask-image: radial-gradient(circle at 50% 40%, black 25%, transparent 75%);
  }

  .hero-content {
    position: relative;
    z-index: 3;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    width: 100%;
  }

  .eyebrow-container {
    margin-bottom: 22px;
    max-width: 100%;
    animation: cinemaReveal 0.8s cubic-bezier(0.16, 1, 0.3, 1) 0.05s both;
  }

  .eyebrow-pill {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background-color: rgba(7, 8, 10, 0.8);
    backdrop-filter: blur(16px);
    -webkit-backdrop-filter: blur(16px);
    border: 1px solid rgba(255, 99, 99, 0.25);
    padding: 5px 14px;
    border-radius: var(--radius-pills);
    box-shadow: 0 0 20px rgba(255, 99, 99, 0.12), var(--shadow-key-subtle);
    max-width: 90vw;
    transition: border-color 0.2s ease, box-shadow 0.2s ease;
  }

  .eyebrow-pill:hover {
    border-color: rgba(255, 99, 99, 0.5);
    box-shadow: 0 0 28px rgba(255, 99, 99, 0.22);
  }

  .pulse-dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background-color: var(--color-coral-pulse);
    box-shadow: 0 0 8px var(--color-coral-pulse);
    flex-shrink: 0;
  }

  .eyebrow-text {
    font-size: 11px;
    color: var(--color-mist);
    letter-spacing: 0.8px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  /* Majestic Holographic Shimmer Headline */
  .hero-headline {
    font-size: clamp(34px, 6.5vw, 62px);
    font-weight: 500;
    line-height: 1.12;
    letter-spacing: -0.6px;
    background: linear-gradient(
      115deg,
      #ffffff 0%,
      #ffffff 38%,
      #ff7878 54%,
      #8ab9ff 72%,
      #ffffff 88%,
      #ffffff 100%
    );
    background-size: 250% 100%;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    animation: textShimmer 9s cubic-bezier(0.4, 0, 0.2, 1) infinite,
               cinemaReveal 0.9s cubic-bezier(0.16, 1, 0.3, 1) 0.15s both;
    margin-bottom: 22px;
    max-width: 920px;
  }

  @keyframes textShimmer {
    0%, 20% {
      background-position: 0% 50%;
    }
    60%, 80% {
      background-position: 100% 50%;
    }
    100% {
      background-position: 0% 50%;
    }
  }

  .hero-subheadline {
    font-size: clamp(14px, 2.5vw, 16.5px);
    font-weight: 400;
    line-height: 1.65;
    color: var(--color-ash);
    max-width: 600px;
    margin-bottom: 34px;
    animation: cinemaReveal 0.9s cubic-bezier(0.16, 1, 0.3, 1) 0.25s both;
  }

  .hero-actions {
    display: flex;
    align-items: center;
    gap: 14px;
    margin-bottom: 26px;
    width: 100%;
    justify-content: center;
    animation: cinemaReveal 0.9s cubic-bezier(0.16, 1, 0.3, 1) 0.35s both;
  }

  .hero-btn {
    padding: 10px 20px;
    font-size: 13.5px;
    transition: transform 0.22s cubic-bezier(0.16, 1, 0.3, 1), box-shadow 0.22s ease, background-color 0.22s ease;
  }

  .hero-btn:hover {
    transform: translateY(-2px);
  }

  .hero-btn-glow {
    position: relative;
    box-shadow: 0 0 24px rgba(255, 255, 255, 0.2), var(--shadow-btn-lift);
  }

  .hero-btn-glow:hover {
    box-shadow: 0 0 32px rgba(255, 255, 255, 0.35), 0 8px 24px rgba(0, 0, 0, 0.4);
  }

  .hero-star-pill {
    background: rgba(255, 99, 99, 0.15);
    border: 1px solid rgba(255, 99, 99, 0.25);
    color: var(--color-coral);
    padding: 2px 7px;
    border-radius: 9999px;
    font-size: 11px;
    letter-spacing: 0.3px;
    transition: all 0.2s ease;
  }

  .hero-btn:hover .hero-star-pill {
    background: rgba(255, 99, 99, 0.25);
    border-color: rgba(255, 99, 99, 0.45);
    box-shadow: 0 0 10px rgba(255, 99, 99, 0.3);
  }

  /* Terminal Command Well */
  .terminal-command-well {
    display: inline-flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
    background-color: var(--color-obsidian);
    border: 1px solid rgba(255, 99, 99, 0.28);
    border-radius: var(--radius-inputs);
    padding: 10px 14px;
    cursor: pointer;
    margin-bottom: 32px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.6), 
                0 0 16px rgba(255, 99, 99, 0.1),
                rgba(255, 255, 255, 0.05) 0px 1px 0px 0px inset;
    transition: border-color 0.22s ease, background-color 0.22s ease, transform 0.22s cubic-bezier(0.16, 1, 0.3, 1), box-shadow 0.22s ease;
    width: 100%;
    max-width: 440px;
    animation: cinemaReveal 0.9s cubic-bezier(0.16, 1, 0.3, 1) 0.45s both;
  }

  .terminal-command-well:hover {
    border-color: rgba(255, 99, 99, 0.55);
    background-color: #16171b;
    transform: translateY(-2px);
    box-shadow: 0 12px 32px -4px rgba(0, 0, 0, 0.7), 0 0 24px rgba(255, 99, 99, 0.22);
  }

  .cmd-content-left {
    position: relative;
    z-index: 1;
    display: flex;
    align-items: center;
    gap: 8px;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    min-width: 0;
  }

  .cmd-prompt {
    color: var(--color-coral-pulse);
    font-size: 13px;
    flex-shrink: 0;
  }

  .cmd-text {
    color: var(--color-pure-white);
    font-size: 12.5px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .cmd-badge {
    position: relative;
    z-index: 1;
    color: var(--color-smoke);
    font-size: 10px;
    letter-spacing: 0.5px;
    padding-left: 8px;
    border-left: 1px solid var(--color-slate);
    flex-shrink: 0;
  }

  /* Cinema Blur-In + Scale Entrance Keyframe */
  @keyframes cinemaReveal {
    from {
      opacity: 0;
      filter: blur(14px);
      transform: translateY(28px) scale(0.96);
    }
    to {
      opacity: 1;
      filter: blur(0px);
      transform: translateY(0) scale(1);
    }
  }

  .hero-meta-strip {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: center;
    gap: 10px;
    font-size: 11.5px;
    color: var(--color-smoke);
    animation: cinemaReveal 1s cubic-bezier(0.16, 1, 0.3, 1) 0.55s both;
  }

  .divider {
    color: var(--color-slate);
  }

  @media (prefers-reduced-motion: reduce) {
    .eyebrow-container,
    .hero-headline,
    .hero-subheadline,
    .hero-actions,
    .terminal-command-well,
    .hero-meta-strip,
    .glow-blue,
    .glow-coral-diagonal,
    .glow-coral-accent,
    .cosmic-aura-ring,
    .hero-canvas {
      animation: none !important;
      opacity: 1 !important;
      transform: none !important;
      filter: none !important;
    }
  }

  @media (max-width: 768px) {
    .hero-section {
      padding-top: 100px;
      padding-bottom: 40px;
    }

    .hero-actions {
      flex-direction: column;
      max-width: 320px;
    }

    .hero-btn {
      width: 100%;
    }

    .hero-meta-strip {
      font-size: 10.5px;
      gap: 6px;
    }
  }
</style>
