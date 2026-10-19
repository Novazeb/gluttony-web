<script lang="ts">
  let copyState = $state<'idle' | 'copied'>('idle');
  const installCmd = '/plugin install gluttony@gluttony';

  function copyCommand() {
    navigator.clipboard.writeText(installCmd);
    copyState = 'copied';
    setTimeout(() => {
      copyState = 'idle';
    }, 2000);
  }
</script>

<section class="hero-section">
  <!-- Atmospheric backdrop: Raycast cinematic red/blue geometry -->
  <div class="hero-atmosphere" aria-hidden="true">
    <div class="glow-blue"></div>
    <div class="glow-coral-diagonal"></div>
    <div class="glow-coral-accent"></div>
    <div class="mesh-grid"></div>
  </div>

  <div class="hero-content container">
    <!-- Eyebrow Pill -->
    <div class="eyebrow-container">
      <div class="eyebrow-pill">
        <span class="pulse-dot"></span>
        <span class="eyebrow-text font-mono">GLUTTONY v2.2.0 · AUTONOMOUS AGENT PROTOCOL</span>
      </div>
    </div>

    <!-- 56px / 400 regular weight signature headline -->
    <h1 class="hero-headline">
      Maximum signal, zero fluff.<br />
      Relentless execution.
    </h1>

    <!-- Subheadline in #9c9c9d -->
    <p class="hero-subheadline">
      High-precision lean engineering mode for autonomous coding agents.
      Enforces YAGNI, eliminates AI boilerplate, preserves contracts, and stops UI slop before it enters your codebase.
    </p>

    <!-- CTAs & Action buttons -->
    <div class="hero-actions">
      <button onclick={copyCommand} class="btn-mist hero-btn">
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
      </a>
    </div>

    <!-- Command strip preview -->
    <div class="terminal-command-well" onclick={copyCommand} role="button" tabindex="0" onkeydown={(e) => e.key === 'Enter' && copyCommand()}>
      <span class="cmd-prompt font-mono">$</span>
      <span class="cmd-text font-mono">{installCmd}</span>
      <span class="cmd-badge font-mono">{copyState === 'copied' ? 'COPIED' : 'CLICK TO COPY'}</span>
    </div>

    <!-- Raycast Footer Meta Strip -->
    <div class="hero-meta-strip font-mono">
      <span>v2.2.0</span>
      <span class="divider">|</span>
      <span>YAGNI by Default</span>
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
    min-height: 85vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding-top: 140px;
    padding-bottom: 80px;
    overflow: hidden;
  }

  /* Atmospheric red/blue geometric artwork */
  .hero-atmosphere {
    position: absolute;
    inset: 0;
    pointer-events: none;
    z-index: 0;
    overflow: hidden;
  }

  .glow-blue {
    position: absolute;
    top: 20%;
    left: 50%;
    transform: translate(-50%, -40%);
    width: 800px;
    height: 480px;
    background: radial-gradient(circle, rgba(4, 63, 150, 0.45) 0%, rgba(6, 18, 37, 0.15) 60%, rgba(4, 5, 6, 0) 80%);
    filter: blur(50px);
  }

  .glow-coral-diagonal {
    position: absolute;
    top: 30%;
    left: 45%;
    transform: translate(-50%, -50%) rotate(-25deg);
    width: 650px;
    height: 120px;
    background: linear-gradient(90deg, transparent 0%, rgba(255, 99, 99, 0.32) 40%, rgba(255, 99, 99, 0.15) 75%, transparent 100%);
    filter: blur(48px);
  }

  .glow-coral-accent {
    position: absolute;
    top: 38%;
    left: 55%;
    width: 280px;
    height: 90px;
    background: rgba(255, 99, 99, 0.22);
    filter: blur(60px);
  }

  .mesh-grid {
    position: absolute;
    inset: 0;
    background-image: linear-gradient(to right, rgba(255, 255, 255, 0.02) 1px, transparent 1px),
                      linear-gradient(to bottom, rgba(255, 255, 255, 0.02) 1px, transparent 1px);
    background-size: 40px 40px;
    mask-image: radial-gradient(circle at 50% 40%, black 20%, transparent 70%);
    -webkit-mask-image: radial-gradient(circle at 50% 40%, black 20%, transparent 70%);
  }

  .hero-content {
    position: relative;
    z-index: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
  }

  .eyebrow-container {
    margin-bottom: 24px;
  }

  .eyebrow-pill {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background-color: var(--color-ink);
    border: 1px solid var(--color-border);
    padding: 4px 12px;
    border-radius: var(--radius-pills);
    box-shadow: var(--shadow-key-subtle);
  }

  .pulse-dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background-color: var(--color-coral-pulse);
    box-shadow: 0 0 8px var(--color-coral-pulse);
  }

  .eyebrow-text {
    font-size: 11px;
    color: var(--color-ash);
    letter-spacing: 0.8px;
  }

  /* Raycast Signature Headline: 56px Inter regular weight 400 with letter-spacing */
  .hero-headline {
    font-size: 56px;
    font-weight: 400;
    line-height: 1.15;
    letter-spacing: 0.22px;
    color: var(--color-pure-white);
    margin-bottom: 24px;
    max-width: 900px;
  }

  .hero-subheadline {
    font-size: 16px;
    font-weight: 400;
    line-height: 1.6;
    color: var(--color-ash);
    max-width: 580px;
    margin-bottom: 36px;
  }

  .hero-actions {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 24px;
  }

  .hero-btn {
    padding: 10px 18px;
    font-size: 14px;
  }

  .terminal-command-well {
    display: inline-flex;
    align-items: center;
    gap: 12px;
    background-color: var(--color-obsidian);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-inputs);
    padding: 10px 16px;
    cursor: pointer;
    margin-bottom: 32px;
    box-shadow: rgba(0, 0, 0, 0.4) 0px 2px 4px 0px, rgba(255, 255, 255, 0.05) 0px 1px 0px 0px inset;
    transition: border-color 0.15s ease, background-color 0.15s ease;
  }

  .terminal-command-well:hover {
    border-color: rgba(255, 255, 255, 0.25);
    background-color: #151619;
  }

  .cmd-prompt {
    color: var(--color-coral-pulse);
    font-size: 13px;
  }

  .cmd-text {
    color: var(--color-pure-white);
    font-size: 13px;
  }

  .cmd-badge {
    color: var(--color-smoke);
    font-size: 10px;
    letter-spacing: 0.5px;
    padding-left: 8px;
    border-left: 1px solid var(--color-slate);
  }

  .hero-meta-strip {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: center;
    gap: 12px;
    font-size: 12px;
    color: var(--color-smoke);
  }

  .divider {
    color: var(--color-slate);
  }

  @media (max-width: 768px) {
    .hero-headline {
      font-size: 36px;
    }

    .hero-actions {
      flex-direction: column;
      width: 100%;
    }

    .hero-btn {
      width: 100%;
    }

    .terminal-command-well {
      width: 100%;
      justify-content: center;
      flex-wrap: wrap;
    }
  }
</style>
