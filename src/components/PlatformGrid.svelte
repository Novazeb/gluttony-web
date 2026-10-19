<script lang="ts">
  const platforms = [
    {
      name: 'Claude Code',
      category: 'Anthropic CLI',
      install: '/plugin install gluttony@gluttony',
      desc: 'Automatic session load via CLAUDE.md & slash commands.',
      badge: 'OFFICIAL',
    },
    {
      name: 'OpenAI Codex',
      category: 'Agent Harness',
      install: 'codex plugin add Novazeb/gluttony',
      desc: 'Native AGENTS.md protocol & developer manifest support.',
      badge: 'MARKETPLACE',
    },
    {
      name: 'Google Antigravity',
      category: 'Google Agentic IDE',
      install: 'antigravity plugin install Novazeb/gluttony',
      desc: 'Pre-loaded skills, sidecar viewers & GEMINI.md rules.',
      badge: 'BUILTIN',
    },
    {
      name: 'Cursor IDE',
      category: 'AI Code Editor',
      install: 'auto-applied via .cursor/rules',
      desc: 'Always-on rule engine with zero prompt overhead.',
      badge: '.MDC RULES',
    },
    {
      name: 'Windsurf IDE',
      category: 'Flow-based Editor',
      install: 'auto-applied via .windsurf/rules',
      desc: 'Cascade agent integration preserving project contracts.',
      badge: 'ALWAYS-ON',
    },
    {
      name: 'Cline / Roo-Code',
      category: 'Autonomous Agent',
      install: 'auto-applied via .clinerules',
      desc: 'Tight autonomous verification & destructive safeguards.',
      badge: 'SYSTEM PROMPT',
    },
    {
      name: 'OpenClaw',
      category: 'Agent Framework',
      install: 'openclaw skill add gluttony',
      desc: 'Complete SSOT skill compilation & execution harness.',
      badge: 'SKILL',
    },
    {
      name: 'Devin',
      category: 'Cognitive Engine',
      install: 'configured via .devin-plugin',
      desc: 'Autonomous engineering mode with YAGNI enforcement.',
      badge: 'PLUGIN',
    },
    {
      name: 'Kiro Steering',
      category: 'AWS Harness',
      install: 'configured via .kiro/steering',
      desc: 'Autonomous steering directives and safety gates.',
      badge: 'STEERING',
    },
  ];

  let copiedIndex = $state<number | null>(null);

  function copyInstall(cmd: string, idx: number) {
    navigator.clipboard.writeText(cmd);
    copiedIndex = idx;
    setTimeout(() => {
      copiedIndex = null;
    }, 2000);
  }
</script>

<section id="platforms" class="platforms-section container">
  <!-- Section Header -->
  <div class="section-header">
    <span class="badge-tag">UNIVERSAL COMPILATION</span>
    <h2 class="section-title">One Rule Source. 14+ Platforms.</h2>
    <p class="section-desc">
      Gluttony compiles from a Single Source of Truth into native rule formats across all modern AI environments.
    </p>
  </div>

  <!-- Extension Tile Grid (Raycast 8px padding / 1px border / tight 8-12px gap) -->
  <div class="platforms-grid">
    {#each platforms as p, idx}
      <div
        class="platform-tile key-card"
        onclick={() => copyInstall(p.install, idx)}
        role="button"
        tabindex="0"
        onkeydown={(e) => e.key === 'Enter' && copyInstall(p.install, idx)}
      >
        <div class="tile-top">
          <div class="tile-header-left">
            <h3 class="platform-name">{p.name}</h3>
            <span class="platform-category font-mono">{p.category}</span>
          </div>
          <span class="badge-tag">{p.badge}</span>
        </div>

        <p class="platform-desc">{p.desc}</p>

        <div class="tile-install font-mono">
          <span class="install-text">{p.install}</span>
          <span class="copy-hint font-mono">{copiedIndex === idx ? 'COPIED' : 'COPY'}</span>
        </div>
      </div>
    {/each}
  </div>
</section>

<style>
  .platforms-section {
    padding-top: var(--spacing-80);
    padding-bottom: var(--spacing-80);
  }

  .section-header {
    text-align: center;
    margin-bottom: 48px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 12px;
  }

  .section-title {
    font-size: 32px;
    font-weight: 500;
    color: var(--color-pure-white);
  }

  .section-desc {
    font-size: 16px;
    color: var(--color-ash);
    max-width: 560px;
  }

  /* Raycast 3-column Extension Tile grid */
  .platforms-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 16px;
  }

  .platform-tile {
    padding: 20px;
    display: flex;
    flex-direction: column;
    gap: 12px;
    background-color: var(--color-ink);
    cursor: pointer;
    transition: transform 0.15s ease, border-color 0.15s ease;
  }

  .platform-tile:hover {
    transform: translateY(-2px);
    border-color: rgba(255, 255, 255, 0.25);
  }

  .tile-top {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
  }

  .platform-name {
    font-size: 16px;
    font-weight: 500;
    color: var(--color-pure-white);
  }

  .platform-category {
    font-size: 11px;
    color: var(--color-smoke);
    display: block;
    margin-top: 2px;
  }

  .platform-desc {
    font-size: 13.5px;
    color: var(--color-ash);
    line-height: 1.5;
  }

  .tile-install {
    margin-top: auto;
    background-color: var(--color-obsidian);
    border: 1px solid rgba(255, 255, 255, 0.06);
    border-radius: var(--radius-inputs);
    padding: 6px 10px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 11px;
    color: var(--color-mist);
  }

  .install-text {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    max-width: 80%;
  }

  .copy-hint {
    font-size: 9.5px;
    color: var(--color-smoke);
  }

  @media (max-width: 960px) {
    .platforms-grid {
      grid-template-columns: repeat(2, 1fr);
    }
  }

  @media (max-width: 600px) {
    .platforms-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
