<script lang="ts">
  import { onMount } from 'svelte';
  import CeknetWidget from './CeknetWidget.svelte';

  type PlatformKey = 'claude' | 'cursor' | 'antigravity' | 'codex' | 'windsurf';

  const harnessOptions: Record<PlatformKey, { name: string; cmd: string; short: string }> = {
    claude: {
      name: 'Claude Code',
      cmd: '/plugin install gluttony@gluttony',
      short: 'CLAUDE.md',
    },
    cursor: {
      name: 'Cursor',
      cmd: 'curl -fsSL https://raw.githubusercontent.com/Novazeb/gluttony/main/.cursor/rules/gluttony.mdc -o .cursor/rules/gluttony.mdc',
      short: '.cursorrules',
    },
    antigravity: {
      name: 'Antigravity',
      cmd: 'antigravity plugin install Novazeb/gluttony',
      short: 'GEMINI.md',
    },
    codex: {
      name: 'Codex',
      cmd: 'codex plugin add Novazeb/gluttony',
      short: 'AGENTS.md',
    },
    windsurf: {
      name: 'Windsurf',
      cmd: 'curl -fsSL https://raw.githubusercontent.com/Novazeb/gluttony/main/.windsurf/rules/gluttony.md -o .windsurf/rules/gluttony.md',
      short: '.windsurf',
    },
  };

  let selectedHarness = $state<PlatformKey>('claude');
  let copyState = $state<'idle' | 'copied'>('idle');
  let starCount = $state<number | null>(null);

  let currentCmd = $derived(harnessOptions[selectedHarness].cmd);

  let heroSectionEl: HTMLElement;
  let canvasEl: HTMLCanvasElement;
  let mouseX = -9999;
  let mouseY = -9999;

  function onHeroMouseMove(e: MouseEvent) {
    if (!heroSectionEl) return;
    const rect = heroSectionEl.getBoundingClientRect();
    mouseX = e.clientX - rect.left;
    mouseY = e.clientY - rect.top;
    heroSectionEl.style.setProperty('--mouse-x', `${mouseX}px`);
    heroSectionEl.style.setProperty('--mouse-y', `${mouseY}px`);
  }

  function copyCurrentCommand() {
    navigator.clipboard.writeText(currentCmd);
    copyState = 'copied';
    setTimeout(() => {
      copyState = 'idle';
    }, 2200);
  }

  onMount(async () => {
    // Keyboard shortcut Cmd+K / Ctrl+K to copy active command
    const handleKeydown = (e: KeyboardEvent) => {
      if ((e.metaKey || e.ctrlKey) && e.key.toLowerCase() === 'k') {
        e.preventDefault();
        copyCurrentCommand();
      }
    };
    window.addEventListener('keydown', handleKeydown);

    // Live GitHub stars fetch with fallback
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

    // Stardust Canvas with natural vertical edge fading and subtle pointer interaction
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

    const colors = ['#38bdf8', '#ff9f30', '#f0f0f0', '#00ff9d'];
    const pCount = Math.min(32, Math.floor((w * h) / 36000));
    const particles = Array.from({ length: pCount }, () => ({
      x: Math.random() * w,
      y: Math.random() * (h * 0.7) + 20,
      vx: (Math.random() - 0.5) * 0.28,
      vy: (Math.random() - 0.5) * 0.28,
      radius: Math.random() * 1.5 + 0.6,
      baseAlpha: Math.random() * 0.45 + 0.2,
      alpha: Math.random() * 0.45 + 0.2,
      color: colors[Math.floor(Math.random() * colors.length)],
      phase: Math.random() * Math.PI * 2,
    }));

    const render = () => {
      ctx.clearRect(0, 0, w, h);

      const bottomFadeStart = h * 0.58;

      // Draw faint connections between nearby stardust
      for (let i = 0; i < particles.length; i++) {
        for (let j = i + 1; j < particles.length; j++) {
          const dx = particles[i].x - particles[j].x;
          const dy = particles[i].y - particles[j].y;
          const dist = Math.hypot(dx, dy);
          if (dist < 105) {
            const avgY = (particles[i].y + particles[j].y) / 2;
            let fade = 1;
            if (avgY > bottomFadeStart) {
              fade = Math.max(0, 1 - (avgY - bottomFadeStart) / (h * 0.74 - bottomFadeStart));
            }
            const lineAlpha = (1 - dist / 105) * 0.12 * fade;
            if (lineAlpha > 0.005) {
              ctx.strokeStyle = `rgba(56, 189, 248, ${lineAlpha})`;
              ctx.lineWidth = 0.6;
              ctx.beginPath();
              ctx.moveTo(particles[i].x, particles[i].y);
              ctx.lineTo(particles[j].x, particles[j].y);
              ctx.stroke();
            }
          }
        }
      }

      // Draw and update glowing particles with pointer repulsion
      for (const p of particles) {
        // Pointer reaction
        const dxM = p.x - mouseX;
        const dyM = p.y - mouseY;
        const distM = Math.hypot(dxM, dyM);
        if (distM < 85 && distM > 0) {
          const force = (1 - distM / 85) * 0.6;
          p.vx += (dxM / distM) * force;
          p.vy += (dyM / distM) * force;
        }

        // Velocity damping
        p.vx *= 0.985;
        p.vy *= 0.985;
        p.x += p.vx;
        p.y += p.vy;
        p.phase += 0.02;

        let edgeAlpha = 1;
        if (p.y > bottomFadeStart) {
          edgeAlpha = Math.max(0, 1 - (p.y - bottomFadeStart) / (h * 0.74 - bottomFadeStart));
        }

        p.alpha = (p.baseAlpha + Math.sin(p.phase) * 0.2) * edgeAlpha;

        if (p.x < -10) p.x = w + 10;
        if (p.x > w + 10) p.x = -10;
        if (p.y < 10) p.vy = Math.abs(p.vy);
        if (p.y > h * 0.73) p.vy = -Math.abs(p.vy);

        if (p.alpha > 0.01) {
          ctx.fillStyle = p.color;
          ctx.globalAlpha = Math.max(0, Math.min(1, p.alpha));
          ctx.beginPath();
          ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
          ctx.fill();

          if (p.radius > 1.3) {
            ctx.beginPath();
            ctx.arc(p.x, p.y, p.radius * 2.4, 0, Math.PI * 2);
            ctx.fillStyle = p.color;
            ctx.globalAlpha = p.alpha * 0.15;
            ctx.fill();
          }
        }
      }

      ctx.globalAlpha = 1;
      animId = requestAnimationFrame(render);
    };

    animId = requestAnimationFrame(render);

    return () => {
      cancelAnimationFrame(animId);
      window.removeEventListener('resize', onResize);
      window.removeEventListener('keydown', handleKeydown);
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
</script>

<section
  bind:this={heroSectionEl}
  class="hero-section"
>
  <!-- Atmospheric blueprint wireframe grid -->
  <div class="hero-atmosphere" aria-hidden="true">
    <canvas bind:this={canvasEl} class="hero-canvas"></canvas>
    <div class="mesh-grid"></div>
  </div>

  <!-- Ceknet Internet Quality Checker (Kotak dengan sudut tak tajam di sebelah kanan atas) -->
  <div class="hero-ceknet-anchor">
    <CeknetWidget />
  </div>

  <div class="hero-content container">
    <!-- Eyebrow Tag -->
    <div class="eyebrow-container">
      <div class="eyebrow-pill">
        <span class="pulse-dot"></span>
        <span class="eyebrow-text font-mono">GLUTTONY v2.2.0 · SCHEMATIC PROTOCOL</span>
      </div>
    </div>

    <!-- Technical Display Headline (JetBrains Mono 700) -->
    <h1 class="hero-headline font-mono">
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
      <button onclick={copyCurrentCommand} class="btn-mist hero-btn hero-btn-glow">
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

    <!-- Interactive Platform Harness Tabs Selector -->
    <div class="harness-selector font-mono" role="tablist" aria-label="Select Target AI Harness">
      {#each Object.entries(harnessOptions) as [key, opt]}
        <button
          class="harness-tab-btn"
          class:active={selectedHarness === key}
          onclick={() => selectedHarness = key as PlatformKey}
          role="tab"
          aria-selected={selectedHarness === key}
        >
          <span>{opt.name}</span>
          <span class="harness-badge">{opt.short}</span>
        </button>
      {/each}
    </div>

    <!-- Command strip preview (Clean, crisp, artifact-free with Keyboard shortcut) -->
    <div
      class="terminal-command-well"
      onclick={copyCurrentCommand}
      role="button"
      tabindex="0"
      onkeydown={(e) => (e.key === 'Enter' || e.key === ' ') && copyCurrentCommand()}
      aria-label="Copy installation command for {harnessOptions[selectedHarness].name}"
    >
      <div class="cmd-content-left">
        <span class="cmd-prompt font-mono">$</span>
        <span class="cmd-text font-mono">{currentCmd}</span>
      </div>
      <div class="cmd-right-group font-mono">
        <span class="cmd-shortcut hide-mobile">⌘K</span>
        <span class="cmd-badge" class:copied={copyState === 'copied'}>
          {#if copyState === 'copied'}
            <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3">
              <polyline points="20 6 9 17 4 12"></polyline>
            </svg>
            <span>COPIED</span>
          {:else}
            <span>COPY</span>
          {/if}
        </span>
      </div>
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
    min-height: 80dvh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding-top: 135px;
    padding-bottom: 60px;
    width: 100%;
    max-width: 100vw;
    background-color: var(--color-void-black);
  }

  /* Ceknet Widget Anchor: Positioned at top-right of the landing page */
  .hero-ceknet-anchor {
    position: absolute;
    top: 18px;
    right: 24px;
    z-index: 30;
  }

  /* Atmosphere: Blueprint Grid Backdrop */
  .hero-atmosphere {
    position: absolute;
    inset: 0;
    pointer-events: none;
    z-index: 0;
    overflow: hidden;
  }

  .hero-canvas {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 1;
    opacity: 0.6;
  }

  /* Mesh Grid: Blueprint technical grid */
  .mesh-grid {
    position: absolute;
    inset: 0;
    background-image: 
      linear-gradient(to right, rgba(28, 43, 58, 0.7) 1px, transparent 1px),
      linear-gradient(to bottom, rgba(28, 43, 58, 0.7) 1px, transparent 1px);
    background-size: 32px 32px;
    mask-image: radial-gradient(ellipse 65% 55% at 50% 40%, black 30%, transparent 80%);
    -webkit-mask-image: radial-gradient(ellipse 65% 55% at 50% 40%, black 30%, transparent 80%);
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
    margin-bottom: 20px;
    max-width: 100%;
  }

  .eyebrow-pill {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background-color: var(--color-ink);
    border: 1px solid var(--color-border);
    padding: 6px 14px;
    border-radius: 0px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
    max-width: 90vw;
    transition: border-color 150ms ease;
  }

  .eyebrow-pill:hover {
    border-color: var(--color-coral-pulse);
  }

  .pulse-dot {
    width: 6px;
    height: 6px;
    border-radius: 0px;
    background-color: var(--color-coral-pulse);
    box-shadow: 0 0 6px var(--color-coral-pulse);
    flex-shrink: 0;
  }

  .eyebrow-text {
    font-size: 11px;
    font-weight: 500;
    color: var(--color-ash);
    letter-spacing: 0.08em;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  /* Engineering Blueprint Display Headline */
  .hero-headline {
    font-size: clamp(2.5rem, 5vw, 4rem);
    font-weight: 700;
    line-height: 1.15;
    letter-spacing: -0.02em;
    color: var(--color-pure-white);
    margin-bottom: 20px;
    max-width: 920px;
  }

  .hero-subheadline {
    font-size: clamp(0.95rem, 2vw, 1.05rem);
    font-weight: 400;
    line-height: 1.6;
    letter-spacing: -0.01em;
    color: var(--color-ash);
    max-width: 64ch;
    margin-bottom: 28px;
  }

  .hero-actions {
    display: flex;
    align-items: center;
    gap: 14px;
    margin-bottom: 24px;
    width: 100%;
    justify-content: center;
  }

  .hero-btn {
    padding: 10px 20px;
    font-size: 0.875rem;
    border-radius: 0px;
    transition: background-color 150ms ease, transform 150ms ease;
  }

  .hero-btn:hover {
    transform: translateY(-1px);
  }

  .hero-btn:active {
    transform: translateY(1px);
  }

  .hero-btn-glow {
    position: relative;
    box-shadow: var(--shadow-btn-lift);
  }

  .hero-star-pill {
    background: rgba(255, 159, 48, 0.12);
    border: 1px solid var(--color-border);
    color: var(--color-coral-pulse);
    padding: 2px 7px;
    border-radius: 0px;
    font-size: 11px;
    letter-spacing: 0.3px;
    transition: all 150ms ease;
  }

  .hero-btn:hover .hero-star-pill {
    border-color: var(--color-coral-pulse);
  }

  /* Harness Selector: 0px sharp rectangular tabs */
  .harness-selector {
    display: flex;
    align-items: center;
    gap: 6px;
    margin-bottom: 16px;
    max-width: 100%;
    overflow-x: auto;
    scrollbar-width: none;
    padding: 2px 4px;
  }

  .harness-selector::-webkit-scrollbar {
    display: none;
  }

  .harness-tab-btn {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    background: var(--color-ink);
    border: 1px solid var(--color-border);
    color: var(--color-ash);
    font-size: 11.5px;
    padding: 6px 12px;
    border-radius: 0px;
    cursor: pointer;
    transition: all 150ms ease;
    white-space: nowrap;
  }

  .harness-tab-btn:hover {
    color: var(--color-pure-white);
    border-color: var(--color-ash);
  }

  .harness-tab-btn.active {
    background: #132234;
    color: var(--color-coral-pulse);
    border-color: var(--color-coral-pulse);
  }

  .harness-badge {
    font-size: 9.5px;
    color: var(--color-smoke);
  }

  .harness-tab-btn.active .harness-badge {
    color: var(--color-coral-pulse);
  }

  /* Terminal Command Well: Blueprint Drafting Container */
  .terminal-command-well {
    display: inline-flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
    background-color: var(--color-obsidian);
    border: 1px solid var(--color-border);
    border-radius: 0px;
    padding: 10px 14px;
    cursor: pointer;
    margin-bottom: 32px;
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.4);
    transition: border-color 150ms ease, background-color 150ms ease;
    width: 100%;
    max-width: 520px;
  }

  .terminal-command-well:hover {
    border-color: var(--color-coral-pulse);
    background-color: #0c1826;
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
    color: var(--color-success-green);
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

  .cmd-right-group {
    display: flex;
    align-items: center;
    gap: 8px;
    flex-shrink: 0;
  }

  .cmd-shortcut {
    font-size: 10px;
    color: var(--color-smoke);
    border: 1px solid var(--color-border);
    border-radius: 0px;
    padding: 2px 6px;
    background: var(--color-ink);
  }

  .cmd-badge {
    position: relative;
    z-index: 1;
    display: inline-flex;
    align-items: center;
    gap: 4px;
    color: var(--color-smoke);
    font-size: 10.5px;
    letter-spacing: 0.5px;
    padding-left: 8px;
    border-left: 1px solid var(--color-border);
    transition: color 150ms ease;
  }

  .cmd-badge.copied {
    color: var(--color-success-green);
    font-weight: 600;
  }

  .hero-meta-strip {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: center;
    gap: 12px;
    font-size: 11px;
    color: var(--color-smoke);
    letter-spacing: 0.05em;
  }

  .divider {
    color: var(--color-border);
  }

  @media (max-width: 1099px) {
    .hero-section {
      padding-top: 155px;
    }

    .hero-ceknet-anchor {
      top: 74px;
      right: 18px;
    }
  }

  @media (max-width: 768px) {
    .hero-section {
      padding-top: 195px;
      padding-bottom: 40px;
    }

    .hero-ceknet-anchor {
      top: 62px;
      right: 14px;
    }

    .hero-actions {
      flex-direction: column;
      max-width: 320px;
    }

    .hero-btn {
      width: 100%;
    }

    .hide-mobile {
      display: none;
    }

    .hero-meta-strip {
      font-size: 10.5px;
      gap: 6px;
    }
  }
</style>
