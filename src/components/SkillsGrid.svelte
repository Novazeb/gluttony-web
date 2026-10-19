<script lang="ts">
  const skills = [
    {
      name: 'gluttony',
      version: 'v2.2.0',
      badge: 'CORE ENGINE',
      title: 'Lean Software Architecture & YAGNI',
      desc: 'The best code is the code never written. Rejects speculative complexity, single-implementor interfaces, and premature wrappers. Leads on line 1 with direct technical diffs, enforcing standard library and native platform features first.',
      highlights: [
        'Answer-First on line 1 with zero pleasantries',
        '3 Intensities: lite, full (default), and strict',
        'Auto-escalates on auth, payments, migrations, public APIs',
        'Absolute safety gates for destructive terminal commands'
      ],
      cmd: '/gluttony [lite|full|strict]',
      iconPath: 'M13 10V3L4 14h7v7l9-11h-7z'
    },
    {
      name: 'gluttony-refactor',
      version: 'v2.2.0',
      badge: 'NEW SUB-SKILL',
      title: 'Safe Restructuring & Contract Integrity',
      desc: 'Behavior-preserving refactoring bracketed by rigorous verification. Maps caller blast radii, runs ground-truth tests before touching code, applies surgical structural diffs (extract, inline, rename, move, consolidate), and proves zero regression.',
      highlights: [
        '4-Phase Protocol: Investigate → Baseline → Transform → Verify',
        'Contract preservation: 100% backward compatibility guaranteed',
        'Atomic operations: exactly one transformation per commit unit',
        'Detects caller blast radius before structural editing'
      ],
      cmd: '/gluttony-refactor [path]',
      iconPath: 'M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15'
    },
    {
      name: 'gluttony-audit',
      version: 'v2.2.0',
      badge: 'PASSIVE SCANNER',
      title: 'Bloat Hunter & Redundant Dependency Auditor',
      desc: 'Strictly read-only codebase auditor. Scans workspace or modified diffs for YAGNI violations, single-implementor abstractions, unneeded third-party libraries (lodash, moment, axios), and AI comment slop without modifying a single file.',
      highlights: [
        'Strictly read-only: zero mutations to source files',
        'Axis 1: Single-implementor interfaces & pointless indirection',
        'Axis 2: Replaces third-party packages with native stdlib equivalents',
        'Outputs a single-line-per-finding ranked deletion punch-list'
      ],
      cmd: '/gluttony-audit [path]',
      iconPath: 'M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4'
    },
    {
      name: 'gluttony-ui',
      version: 'v2.2.0',
      badge: 'UI & ACCESSIBILITY',
      title: 'High-Precision Web Interface Hygiene',
      desc: 'Eliminates monotonic cyan-purple generic AI templates and unmotivated glow orbs. Enforces the Purpose Test, WCAG AA contrast (≥ 4.5:1), visible keyboard focus rings, and mobile-friendly ≥ 44x44px touch targets.',
      highlights: [
        'The Purpose Test: Every visual technique must serve hierarchy',
        'Color discipline: 2-3 core colors + 1 deliberate accent',
        'WCAG AA compliance: automated contrast ratio validation',
        'Bundled zero-dependency contrast utility script'
      ],
      cmd: '/gluttony-ui [lite|full|strict]',
      iconPath: 'M4 5a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1V5zM4 13a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H5a1 1 0 01-1-1v-6zM16 13a1 1 0 011-1h2a1 1 0 011 1v6a1 1 0 01-1 1h-2a1 1 0 01-1-1v-6z'
    }
  ];

  let copiedIdx = $state<number | null>(null);

  function copyCmd(cmd: string, idx: number) {
    navigator.clipboard.writeText(cmd);
    copiedIdx = idx;
    setTimeout(() => {
      copiedIdx = null;
    }, 2000);
  }
</script>

<section id="skills" class="skills-section container">
  <!-- Section Header -->
  <div class="section-header">
    <span class="badge-tag">CANONICAL ARSENAL</span>
    <h2 class="section-title">Engineered Sub-Skills</h2>
    <p class="section-desc">
      Each sub-skill tackles a distinct failure mode in AI-generated codebases.
    </p>
  </div>

  <!-- 2x2 Grid of Feature Cards with Key Shadow -->
  <div class="skills-grid">
    {#each skills as skill, idx}
      <div class="skill-card key-card">
        <!-- Top bar with circular icon container and badge -->
        <div class="card-top">
          <div class="icon-circle">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d={skill.iconPath} stroke-linecap="round" stroke-linejoin="round"></path>
            </svg>
          </div>
          <div class="card-badges">
            <span class="badge-tag">{skill.badge}</span>
            <span class="badge-version font-mono">{skill.version}</span>
          </div>
        </div>

        <!-- Title & Subtitle -->
        <div class="card-heading">
          <h3 class="skill-name font-mono">{skill.name}</h3>
          <h4 class="skill-title">{skill.title}</h4>
        </div>

        <!-- Body text in #9c9c9d -->
        <p class="skill-desc">{skill.desc}</p>

        <!-- Highlights Checklist -->
        <ul class="highlights-list">
          {#each skill.highlights as item}
            <li class="highlight-item">
              <span class="check-bullet font-mono">›</span>
              <span>{item}</span>
            </li>
          {/each}
        </ul>

        <!-- Footer command strip -->
        <div class="card-footer">
          <button onclick={() => copyCmd(skill.cmd, idx)} class="cmd-pill-strip">
            <span class="font-mono">{skill.cmd}</span>
            <span class="copy-tag font-mono">{copiedIdx === idx ? 'COPIED' : 'COPY'}</span>
          </button>
        </div>
      </div>
    {/each}
  </div>
</section>

<style>
  .skills-section {
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

  .skills-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }

  .skill-card {
    padding: 28px;
    display: flex;
    flex-direction: column;
    gap: 18px;
    background-color: var(--color-ink);
  }

  .card-top {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  /* Raycast 99999px Circular Icon Container */
  .icon-circle {
    width: 44px;
    height: 44px;
    border-radius: var(--radius-circle);
    background-color: var(--color-obsidian);
    border: 1px solid var(--color-border);
    display: flex;
    align-items: center;
    justify-content: center;
    color: var(--color-mist);
    box-shadow: rgba(255, 255, 255, 0.05) 0px 1px 0px 0px inset;
  }

  .card-badges {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .badge-version {
    font-size: 11px;
    color: var(--color-smoke);
  }

  .card-heading {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  .skill-name {
    font-size: 20px;
    font-weight: 500;
    color: var(--color-pure-white);
    letter-spacing: -0.2px;
  }

  .skill-title {
    font-size: 14px;
    font-weight: 400;
    color: var(--color-coral-pulse);
  }

  .skill-desc {
    font-size: 14.5px;
    color: var(--color-ash);
    line-height: 1.6;
  }

  .highlights-list {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 8px;
    padding-top: 8px;
    border-top: 1px solid rgba(255, 255, 255, 0.05);
  }

  .highlight-item {
    display: flex;
    align-items: baseline;
    gap: 8px;
    font-size: 13px;
    color: #b5b6b8;
  }

  .check-bullet {
    color: var(--color-coral-pulse);
    font-weight: bold;
  }

  .card-footer {
    margin-top: auto;
    padding-top: 8px;
  }

  .cmd-pill-strip {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background-color: var(--color-obsidian);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-inputs);
    padding: 8px 12px;
    color: var(--color-pure-white);
    font-size: 12.5px;
    cursor: pointer;
    box-shadow: rgba(0, 0, 0, 0.4) 0px 1px 2px 0px inset;
    transition: all 0.15s ease;
  }

  .cmd-pill-strip:hover {
    border-color: rgba(255, 255, 255, 0.25);
    background-color: #17181c;
  }

  .copy-tag {
    font-size: 10px;
    color: var(--color-smoke);
    letter-spacing: 0.5px;
  }

  @media (max-width: 860px) {
    .skills-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
