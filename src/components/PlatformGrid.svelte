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
    width: 100%;
    box-sizing: border-box;
  }

  .section-header {
    text-align: center;
    margin-bottom: 36px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 12px;
  }

  .section-title {
    font-size: clamp(24px, 5.5vw, 32px);
    font-weight: 500;
    color: var(--color-pure-white);
  }

  .section-desc {
    font-size: clamp(14px, 2.5vw, 16px);
    color: var(--color-ash);
    max-width: 560px;
  }

  /* Raycast 3-column Extension Tile grid */
  .platforms-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 16px;
    width: 100%;
    box-sizing: border-box;
  }

  .platform-tile {
    padding: 18px;
    display: flex;
    flex-direction: column;
    gap: 10px;
    background-color: var(--color-ink);
    cursor: pointer;
    transition: transform 0.22s cubic-bezier(0.16, 1, 0.3, 1), 
                border-color 0.22s cubic-bezier(0.16, 1, 0.3, 1),
                box-shadow 0.22s cubic-bezier(0.16, 1, 0.3, 1);
    box-sizing: border-box;
    min-width: 0;
  }

  .platform-tile:hover {
    transform: translateY(-3px);
    border-color: rgba(255, 99, 99, 0.3);
    box-shadow: 0 12px 28px -8px rgba(0, 0, 0, 0.7), rgba(255, 99, 99, 0.08) 0 0 16px;
  }

  .tile-top {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 8px;
  }

  .platform-name {
    font-size: 15px;
    font-weight: 500;
    color: var(--color-pure-white);
  }

  .platform-category {
    font-size: 10.5px;
    color: var(--color-smoke);
    display: block;
    margin-top: 2px;
  }

  .platform-desc {
    font-size: 13px;
    color: var(--color-ash);
    line-height: 1.45;
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
    gap: 8px;
    font-size: 11px;
    color: var(--color-mist);
    min-width: 0;
  }

  .install-text {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    max-width: calc(100% - 40px);
  }

  .copy-hint {
    font-size: 9px;
    color: var(--color-smoke);
    flex-shrink: 0;
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

    .platform-tile {
      padding: 16px;
    }
  }
</style>
