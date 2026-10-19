<script lang="ts">
  type CommandKey = 'gluttony' | 'refactor' | 'audit' | 'ui';

  let activeTab = $state<CommandKey>('gluttony');
  let copied = $state(false);

  const commandData: Record<CommandKey, {
    cmd: string;
    label: string;
    tag: string;
    badge: string;
    description: string;
    samplePrompt: string;
    sampleOutput: string;
    rules: string[];
  }> = {
    gluttony: {
      cmd: '/gluttony full',
      label: 'Core Engineering',
      tag: 'SIMPLICITY LADDER · YAGNI',
      badge: 'DEFAULT',
      description: 'Maximum signal, zero conversational fluff. Applies standard library first, native platform features, smallest diff at root cause, and auto-escalates to strict mode on auth/payments.',
      samplePrompt: 'Refactor our rate-limiter service to reduce memory leak in background timers.',
      sampleOutput: `[strict: security/contract boundary touched]
1. Root cause: setInterval timers in TokenBucket leaky on reconnects.
2. Direct fix: replace interval with native setTimeout debouncer and WeakRef cache.
3. Verification: 100,000 synthetic requests passed, memory footprint flat at 14MB.

Diff: -32 lines third-party cron, +6 lines native timer. Backward compatible.`,
      rules: [
        'Line 1 Answer-First: Zero pleasantries or conversational preambles',
        'Simplicity Ladder: Stdlib first, native platform features before packages',
        'Cognitive Cap: Top 5 items prioritized without concealing risk',
        'Auto-Escalation: Escalate to strict on auth, payments, migrations, public APIs',
      ],
    },
    refactor: {
      cmd: '/gluttony-refactor src/services/auth.ts',
      label: 'gluttony-refactor',
      tag: '4-PHASE VERIFICATION BRACKET',
      badge: 'NEW v2.2.0',
      description: 'Behavior-preserving structural restructuring. Brackets every edit with verification: Investigate → Baseline → Transform → Verify. Zero regression guarantee.',
      samplePrompt: 'Extract token parsing and consolidation from auth handlers.',
      sampleOutput: `### Extract Function: parseBearerToken
- Blast radius: 6 callers, 2 files (src/api/auth.ts, src/middleware/jwt.ts)
- Risk: medium (shared request boundary)
- Baseline: 18 unit tests passed, 0 type errors
- Diff: unified duplicated header substring slices into parseBearerToken(req)
- Verification: 18 unit tests re-run, all passed. Zero contract breaks.

Summary: 1 operation applied, 2 files changed, all verifications passed.`,
      rules: [
        'Phase 1 Investigate: Map blast radius and identify all public interfaces',
        'Phase 2 Baseline: Run existing tests and types to establish ground truth',
        'Phase 3 Transform: Smallest structural diff without adding speculative features',
        'Phase 4 Verify: Re-run baseline to prove exact behavioral equivalence',
      ],
    },
    audit: {
      cmd: '/gluttony-audit',
      label: 'gluttony-audit',
      tag: 'PASSIVE BLOAT HUNTER',
      badge: 'READ-ONLY',
      description: 'Strictly passive code auditor scanning for YAGNI violations, single-implementor interfaces, redundant dependencies (lodash, moment, axios), and AI comment slop.',
      samplePrompt: 'Audit repository for over-engineering and bloat.',
      sampleOutput: `### High
- src/utils/date.ts:12 — dependency 'moment' → replace with native Intl.DateTimeFormat
- src/services/wrapper.ts:1-45 — passthrough wrapper with no transformation → call service directly

### Medium
- src/types/user.ts:4 — single-implementor interface IUserService → use concrete class directly

### Low
- src/api/auth.ts:45 — code-paraphrasing comment '// validate token' → remove noise

Summary: 4 findings (2 high, 1 medium, 1 low), estimated ~60 lines pruned, 1 dependency removed.`,
      rules: [
        'Strictly Read-Only: Never modifies files, runs only non-mutating checks',
        'Axis 1 YAGNI: Single-implementor interfaces, unused generic wrappers',
        'Axis 2 Dependency Bloat: Lodash → ES2020+, Axios → Fetch, Moment → Intl',
        'Axis 3 Comment Hygiene: Deletes code-restating comments and ASCII banners',
      ],
    },
    ui: {
      cmd: '/gluttony-ui strict',
      label: 'gluttony-ui',
      tag: 'WCAG AA · PURPOSE TEST',
      badge: 'ACCESSIBLE',
      description: 'Authentic interface hygiene: eliminates cyan-purple generic AI templates, enforces WCAG AA minimum 4.5:1 contrast, visible focus rings, and 44x44px mobile touch targets.',
      samplePrompt: 'Build an accessible confirmation modal for deleting project workspaces.',
      sampleOutput: `1. Purpose Test: matte elevation (#07080a), no gratuitous background glow orbs.
2. WCAG AA Contrast: text #ffffff on background #111214 yields 16.4:1 (target >= 4.5:1 PASS).
3. Focus Trap: Tab key cycles strictly within modal action buttons; Esc dispatches cancel.
4. Mobile Touch Targets: Confirm and Cancel buttons sized 48px height (>= 44px PASS).

Verified via: node script/contrast.js "#ffffff" "#111214" --target=normal`,
      rules: [
        'The Purpose Test: Every visual element must serve functional hierarchy',
        'Color Restraint: 2-3 core colors + 1 deliberate accent, matte elevation',
        'Platform Accessibility: WCAG AA 4.5:1 contrast, 44x44px touch targets',
        'Built-in Contrast Utility: node script/contrast.js integration',
      ],
    },
  };

  function copyCurrentCmd() {
    navigator.clipboard.writeText(commandData[activeTab].cmd);
    copied = true;
    setTimeout(() => {
      copied = false;
    }, 2000);
  }
</script>

<section id="cockpit" class="cockpit-section container">
  <!-- Section Header -->
  <div class="section-header">
    <span class="badge-tag">INTERACTIVE COCKPIT</span>
    <h2 class="section-title">The Gluttony Command Center</h2>
    <p class="section-desc">
      Four purpose-built skills designed to keep autonomous agents lean, correct, and surgical.
    </p>
  </div>

  <!-- Raycast App Window Mockup -->
  <div class="cockpit-window key-card">
    <!-- Window Titlebar -->
    <div class="window-titlebar">
      <div class="traffic-lights">
        <span class="dot close"></span>
        <span class="dot minimize"></span>
        <span class="dot expand"></span>
      </div>
      <div class="titlebar-center">
        <span class="window-title font-mono">gluttony-cockpit</span>
      </div>
      <div class="titlebar-actions">
        <span class="badge-tag">{commandData[activeTab].badge}</span>
      </div>
    </div>

    <!-- Command Search / Selector Bar (Raycast command bar style) -->
    <div class="command-bar-wrapper">
      <div class="command-search-bar">
        <svg class="search-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <circle cx="11" cy="11" r="8"></circle>
          <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
        </svg>

        <div class="command-pills">
          <button
            class="cmd-pill"
            class:active={activeTab === 'gluttony'}
            onclick={() => (activeTab = 'gluttony')}
          >
            <span class="cmd-pill-slash">/</span>gluttony
          </button>
          <button
            class="cmd-pill"
            class:active={activeTab === 'refactor'}
            onclick={() => (activeTab = 'refactor')}
          >
            <span class="cmd-pill-slash">/</span>refactor
          </button>
          <button
            class="cmd-pill"
            class:active={activeTab === 'audit'}
            onclick={() => (activeTab = 'audit')}
          >
            <span class="cmd-pill-slash">/</span>audit
          </button>
          <button
            class="cmd-pill"
            class:active={activeTab === 'ui'}
            onclick={() => (activeTab = 'ui')}
          >
            <span class="cmd-pill-slash">/</span>ui
          </button>
        </div>

        <button onclick={copyCurrentCmd} class="btn-mist cmd-copy-btn">
          {copied ? 'Copied' : 'Copy'}
        </button>
      </div>
    </div>

    <!-- Window Body: Dual-Pane Cockpit -->
    <div class="cockpit-body">
      <!-- Left Pane: Info & Protocol -->
      <div class="cockpit-pane info-pane">
        <div class="pane-header">
          <span class="pane-tag font-mono">{commandData[activeTab].tag}</span>
          <h3 class="pane-title font-mono">{commandData[activeTab].cmd}</h3>
          <p class="pane-desc">{commandData[activeTab].description}</p>
        </div>

        <div class="pane-rules-list">
          <span class="list-label font-mono">ENFORCED RULES:</span>
          {#each commandData[activeTab].rules as rule}
            <div class="rule-item">
              <span class="rule-diamond">◆</span>
              <span class="rule-text">{rule}</span>
            </div>
          {/each}
        </div>
      </div>

      <!-- Right Pane: Terminal Output Mockup -->
      <div class="cockpit-pane terminal-pane">
        <div class="terminal-header font-mono">
          <span class="terminal-tab active">OUTPUT TERMINAL</span>
          <span class="terminal-status text-coral">● LIVE PROTOCOL</span>
        </div>

        <div class="terminal-content font-mono">
          <div class="term-line term-prompt">
            <span class="term-user">user@gluttony</span>:<span class="term-dir">~/repo</span>$ {commandData[activeTab].cmd}
          </div>
          <div class="term-divider"></div>
          <pre class="term-body">{commandData[activeTab].sampleOutput}</pre>
        </div>
      </div>
    </div>
  </div>
</section>

<style>
  .cockpit-section {
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

  /* Raycast window chrome */
  .cockpit-window {
    overflow: hidden;
    background-color: var(--color-ink);
    width: 100%;
    box-sizing: border-box;
  }

  .window-titlebar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 10px 16px;
    border-bottom: 1px solid var(--color-border);
    background-color: #050608;
    gap: 8px;
  }

  .traffic-lights {
    display: flex;
    align-items: center;
    gap: 6px;
    flex-shrink: 0;
  }

  .dot {
    width: 9px;
    height: 9px;
    border-radius: 50%;
  }

  .dot.close { background-color: #ff5f56; }
  .dot.minimize { background-color: #ffbd2e; }
  .dot.expand { background-color: #27c93f; }

  .titlebar-center {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    min-width: 0;
  }

  .window-title {
    font-size: 11px;
    color: var(--color-smoke);
    letter-spacing: 0.5px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .titlebar-actions {
    flex-shrink: 0;
  }

  /* Raycast command search bar */
  .command-bar-wrapper {
    padding: 12px 16px;
    border-bottom: 1px solid var(--color-border);
    background-color: var(--color-void-black);
    width: 100%;
    box-sizing: border-box;
  }

  .command-search-bar {
    display: flex;
    align-items: center;
    gap: 10px;
    background-color: var(--color-obsidian);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-inputs);
    padding: 6px 10px;
    box-shadow: rgba(0, 0, 0, 0.4) 0px 2px 4px 0px inset;
    width: 100%;
    box-sizing: border-box;
    min-width: 0;
  }

  .search-icon {
    color: var(--color-smoke);
    flex-shrink: 0;
  }

  .command-pills {
    display: flex;
    align-items: center;
    gap: 6px;
    flex-grow: 1;
    min-width: 0;
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
    scrollbar-width: none;
  }

  .command-pills::-webkit-scrollbar {
    display: none;
  }

  .cmd-pill {
    background: transparent;
    border: 1px solid transparent;
    color: var(--color-ash);
    font-family: var(--font-mono);
    font-size: 11.5px;
    padding: 4px 8px;
    border-radius: 6px;
    cursor: pointer;
    white-space: nowrap;
    transition: all 0.15s ease;
    flex-shrink: 0;
  }

  .cmd-pill:hover {
    color: var(--color-pure-white);
    background-color: rgba(255, 255, 255, 0.05);
  }

  .cmd-pill.active {
    background-color: #1b1c1e;
    color: var(--color-pure-white);
    border-color: rgba(255, 255, 255, 0.2);
    box-shadow: var(--shadow-key-subtle);
  }

  .cmd-pill-slash {
    color: var(--color-coral-pulse);
    font-weight: 600;
  }

  .cmd-copy-btn {
    padding: 4px 10px;
    font-size: 11.5px;
    flex-shrink: 0;
    min-height: 32px;
  }

  /* Body panes */
  .cockpit-body {
    display: grid;
    grid-template-columns: 1fr 1.25fr;
    min-height: 380px;
    width: 100%;
    box-sizing: border-box;
  }

  .cockpit-pane {
    padding: 20px;
    box-sizing: border-box;
    min-width: 0;
  }

  .info-pane {
    border-right: 1px solid var(--color-border);
    display: flex;
    flex-direction: column;
    gap: 20px;
    background-color: var(--color-ink);
  }

  .pane-tag {
    font-size: 10.5px;
    color: var(--color-coral-pulse);
    letter-spacing: 0.8px;
    display: block;
    margin-bottom: 6px;
  }

  .pane-title {
    font-size: clamp(16px, 3.5vw, 19px);
    font-weight: 500;
    color: var(--color-pure-white);
    margin-bottom: 8px;
    word-break: break-word;
  }

  .pane-desc {
    font-size: 13.5px;
    color: var(--color-ash);
    line-height: 1.55;
  }

  .pane-rules-list {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .list-label {
    font-size: 10.5px;
    color: var(--color-smoke);
    letter-spacing: 0.8px;
    margin-bottom: 2px;
  }

  .rule-item {
    display: flex;
    align-items: flex-start;
    gap: 8px;
    font-size: 12.5px;
    color: var(--color-pure-white);
    line-height: 1.4;
  }

  .rule-diamond {
    color: var(--color-coral-pulse);
    font-size: 9px;
    margin-top: 3px;
    flex-shrink: 0;
  }

  /* Terminal pane */
  .terminal-pane {
    background-color: #050608;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  .terminal-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 11px;
    padding-bottom: 10px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.05);
    margin-bottom: 14px;
    gap: 8px;
  }

  .terminal-tab {
    color: var(--color-ash);
    letter-spacing: 0.5px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .terminal-status {
    font-size: 10px;
    letter-spacing: 0.5px;
    flex-shrink: 0;
  }

  .terminal-content {
    font-size: 12px;
    line-height: 1.55;
    color: var(--color-mist);
    overflow-x: auto;
    max-width: 100%;
  }

  .term-line {
    margin-bottom: 8px;
  }

  .term-prompt {
    color: var(--color-pure-white);
    word-break: break-all;
  }

  .term-user {
    color: var(--color-info-blue);
  }

  .term-dir {
    color: var(--color-success-green);
  }

  .term-divider {
    height: 1px;
    background-color: rgba(255, 255, 255, 0.05);
    margin: 10px 0;
  }

  .term-body {
    white-space: pre-wrap;
    word-break: break-word;
    color: #c9cbcf;
    font-family: var(--font-mono);
    max-width: 100%;
  }

  @media (max-width: 860px) {
    .cockpit-body {
      grid-template-columns: 1fr;
    }

    .info-pane {
      border-right: none;
      border-bottom: 1px solid var(--color-border);
      padding: 16px;
    }

    .terminal-pane {
      padding: 16px;
    }
  }
</style>
