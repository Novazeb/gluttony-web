<script lang="ts">
  type PlatformConfig = {
    id: string;
    name: string;
    badge: string;
    tagline: string;
    installCmd: string;
    filePath: string;
    fileContent: string;
    verificationStep: string;
    tip: string;
  };

  const platformConfigs: PlatformConfig[] = [
    {
      id: 'claude',
      name: 'Claude Code',
      badge: 'CLI PLUGIN',
      tagline: 'Anthropic Official CLI & Terminal Agent',
      installCmd: '/plugin install gluttony@gluttony',
      filePath: 'CLAUDE.md',
      fileContent: `# Gluttony Mode for Claude Code
# Load standard Gluttony rule protocol
[gluttony:full]

1. Answer-First: Direct code or terminal command on line 1. Zero greetings.
2. Numbered Steps: Multi-step work must be numbered. 1 step = 1 bounded action.
3. YAGNI & Lean Code: No speculative abstractions, standard library first.
4. Auto-escalate to [strict] when touching auth, migrations, or public contracts.`,
      verificationStep: 'Run `claude` and test with: "Fix typo in README". Agent must reply with diff on line 1 without conversational greeting.',
      tip: 'Claude Code reads CLAUDE.md on every prompt. Use /gluttony:lite for rapid one-liners.',
    },
    {
      id: 'cursor',
      name: 'Cursor IDE',
      badge: '.MDC RULES',
      tagline: 'Cursor AI Code Editor & Agent Mode',
      installCmd: 'curl -fsSL https://raw.githubusercontent.com/Novazeb/gluttony/main/.cursor/rules/gluttony.mdc -o .cursor/rules/gluttony.mdc',
      filePath: '.cursor/rules/gluttony.mdc',
      fileContent: `---
description: High-precision lean engineering protocol for Cursor AI
globs: *
alwaysApply: true
---
# Gluttony Cursor Rule
- Answer-First: Output solution, command, or diff on line 1.
- YAGNI: Reject speculative interfaces, reuse standard library.
- Bracketed Verification: Non-trivial changes must include test execution.
- Scope Boundary: Never delete comments or refactor untouched code.`,
      verificationStep: 'Open Cursor Composer (Ctrl+I / Cmd+I) and prompt: "Check package.json". Gluttony delivers concise output on line 1.',
      tip: 'Set alwaysApply: true so the rule activates automatically for both Composer and Chat.',
    },
    {
      id: 'antigravity',
      name: 'Antigravity',
      badge: 'BUILTIN / IDE',
      tagline: 'Google Agentic IDE & Multimodal Harness',
      installCmd: 'antigravity plugin install Novazeb/gluttony',
      filePath: 'GEMINI.md',
      fileContent: `# Gluttony - System Instructions for Antigravity

1. Answer-First: Lead directly with technical solution or diff.
2. Numbered Steps: Multi-step work must be numbered.
3. Progressive Disclosure: Top 5 items max for routine summaries.
4. YAGNI & Lean Code: Stdlib first, native platform features.
5. Absolute Safety Gate: Confirm before destructive commands (rm -rf, git reset).`,
      verificationStep: 'Ask Antigravity: "Show status". Agent immediately prints status without conversational warmups.',
      tip: 'Antigravity automatically compiles sidecars and GEMINI.md rules into runtime context.',
    },
    {
      id: 'windsurf',
      name: 'Windsurf',
      badge: 'CASCADE RULE',
      tagline: 'Codeium Flow-based Collaborative IDE',
      installCmd: 'curl -fsSL https://raw.githubusercontent.com/Novazeb/gluttony/main/.windsurf/rules/gluttony.md -o .windsurf/rules/gluttony.md',
      filePath: '.windsurf/rules/gluttony.md',
      fileContent: `# Gluttony Protocol for Windsurf Cascade
Apply to all Cascade chat and inline edit flows:
- Deliver direct answers first, cutting rhetorical openers.
- Preserve existing public contracts and shared schemas.
- Remove redundant boilerplate comments and decorative banners.
- When ambiguity materially affects security, ask a 1-line clarification.`,
      verificationStep: 'Prompt Cascade: "Add healthcheck endpoint". Cascade writes the exact route without speculative helper classes.',
      tip: 'Windsurf respects project-level rules in .windsurf/rules across all team members.',
    },
    {
      id: 'cline',
      name: 'Cline / Roo',
      badge: 'SYSTEM PROMPT',
      tagline: 'Autonomous Autonomous Agent Extension',
      installCmd: 'curl -fsSL https://raw.githubusercontent.com/Novazeb/gluttony/main/.clinerules -o .clinerules',
      filePath: '.clinerules',
      fileContent: `# Gluttony Directives for Cline & Roo-Code
- Answer-First: Give terminal command or code diff on line 1.
- Numbered Steps: 1 step = 1 bounded action.
- Destructive Guard: Explicit confirmation required before executing drop, rm, or force push.
- Stdlib First: Do not add npm/pip packages without explicit justification.`,
      verificationStep: 'Instruct Cline to inspect directory. Cline executes directly without conversational fluff.',
      tip: 'Cline injects .clinerules at the top of the system prompt for maximum constraint compliance.',
    },
    {
      id: 'codex',
      name: 'OpenAI Codex',
      badge: 'AGENTS.MD',
      tagline: 'OpenAI Developer Platform & Agent Harness',
      installCmd: 'codex plugin add Novazeb/gluttony',
      filePath: 'AGENTS.md',
      fileContent: `# Gluttony Agent Rules (Universal AGENTS.md)
Active for all autonomous agents in this repository.
Precedence: Safety Gates > Correctness & Contracts > Lean Code > Communication.

- Answer-First: No greetings or closers.
- Simplicity Ladder: YAGNI -> Reuse local code -> Standard library -> Smallest diff.
- UI Specialization: Min WCAG AA 4.5:1 contrast, visible focus rings, 44px tap targets.`,
      verificationStep: 'Test with Codex CLI: `codex run "refactor utils"`. Output adheres strictly to smallest diff.',
      tip: 'AGENTS.md is the cross-industry standard recognized by Codex, OpenClaw, and GitHub Copilot Workspace.',
    },
    {
      id: 'openclaw',
      name: 'OpenClaw',
      badge: 'FRAMEWORK',
      tagline: 'Composable Multi-Agent Execution Framework',
      installCmd: 'openclaw skill add gluttony',
      filePath: 'claw.config.json',
      fileContent: `{
  "skills": ["gluttony", "gluttony-refactor", "gluttony-ui"],
  "rules": {
    "protocol": "gluttony:full",
    "answerFirst": true,
    "yagni": true,
    "maxSummaryItems": 5
  }
}`,
      verificationStep: 'Run `openclaw check` to verify gluttony skill bundle is resolved and registered.',
      tip: 'OpenClaw allows activating gluttony-refactor and gluttony-ui as specialized sub-agent delegates.',
    },
  ];

  let selectedId = $state<string>('claude');
  let copyState = $state<'idle' | 'install' | 'file'>('idle');

  let activePlatform = $derived.by(() => {
    return platformConfigs.find((p) => p.id === selectedId) || platformConfigs[0];
  });

  function copyInstall(cmd: string) {
    navigator.clipboard.writeText(cmd);
    copyState = 'install';
    setTimeout(() => {
      copyState = 'idle';
    }, 2000);
  }

  function copyFileContent(content: string) {
    navigator.clipboard.writeText(content);
    copyState = 'file';
    setTimeout(() => {
      copyState = 'idle';
    }, 2000);
  }
</script>

<section id="setup" class="setup-section container">
  <!-- Section Header -->
  <div class="section-header">
    <div class="header-eyebrow font-mono">
      <span class="pulse-dot"></span>
      <span>RAPID DEPLOYMENT</span>
    </div>
    <h2 class="section-title">Setup Wizard: Zero-Friction Setup</h2>
    <p class="section-desc">
      Choose your platform. Copy the native config file or run one terminal command to activate Gluttony instantly.
    </p>
  </div>

  <div class="wizard-container key-card">
    <!-- Platform Selector Tabs (Horizontal scroll on mobile) -->
    <div class="tabs-scroll-area">
      <div class="platform-tabs" role="tablist">
        {#each platformConfigs as p}
          <button
            class="tab-btn"
            class:active={selectedId === p.id}
            onclick={() => (selectedId = p.id)}
            role="tab"
            aria-selected={selectedId === p.id}
          >
            <span class="tab-name">{p.name}</span>
            <span class="tab-badge font-mono">{p.badge}</span>
          </button>
        {/each}
      </div>
    </div>

    <!-- Active Platform Details & Code Panel -->
    <div class="wizard-panel">
      <!-- Platform Header Meta -->
      <div class="panel-header">
        <div class="panel-info">
          <div class="panel-title-row">
            <h3 class="panel-title">{activePlatform.name}</h3>
            <span class="badge-tag">{activePlatform.badge}</span>
          </div>
          <p class="panel-subtitle">{activePlatform.tagline}</p>
        </div>

        <!-- 1-Click Install Button -->
        <button
          class="btn-mist install-btn font-mono"
          onclick={() => copyInstall(activePlatform.installCmd)}
        >
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
            <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
          </svg>
          <span>{copyState === 'install' ? 'Copied Command!' : 'Copy Command'}</span>
        </button>
      </div>

      <!-- Quick Command Bar -->
      <div class="quick-cmd-bar">
        <span class="cmd-label font-mono">INSTALL</span>
        <code class="cmd-code font-mono">{activePlatform.installCmd}</code>
      </div>

      <!-- Config File Editor Box -->
      <div class="file-box">
        <div class="file-header">
          <div class="file-title-group">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M13 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V9z"></path>
              <polyline points="13 2 13 9 20 9"></polyline>
            </svg>
            <span class="file-path font-mono">{activePlatform.filePath}</span>
          </div>
          <button
            class="copy-file-btn font-mono"
            onclick={() => copyFileContent(activePlatform.fileContent)}
          >
            {copyState === 'file' ? 'Copied Config!' : 'Copy File Content'}
          </button>
        </div>

        <pre class="file-code font-mono"><code>{activePlatform.fileContent}</code></pre>
      </div>

      <!-- Grid of Verification & Tips -->
      <div class="meta-cards-grid">
        <div class="meta-card">
          <div class="card-label font-mono">
            <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="#ff6363" stroke-width="2.5">
              <polyline points="20 6 9 17 4 12"></polyline>
            </svg>
            <span>SMOKE TEST VERIFICATION</span>
          </div>
          <p class="card-text">{activePlatform.verificationStep}</p>
        </div>

        <div class="meta-card">
          <div class="card-label font-mono">
            <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="#4ade80" stroke-width="2.5">
              <circle cx="12" cy="12" r="10"></circle>
              <line x1="12" y1="16" x2="12" y2="12"></line>
              <line x1="12" y1="8" x2="12.01" y2="8"></line>
            </svg>
            <span>ENGINEER TIP</span>
          </div>
          <p class="card-text">{activePlatform.tip}</p>
        </div>
      </div>
    </div>
  </div>
</section>

<style>
  .setup-section {
    padding-top: 40px;
    padding-bottom: 40px;
  }

  .header-eyebrow {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    font-size: 11px;
    font-weight: 500;
    color: var(--color-coral);
    background: rgba(255, 99, 99, 0.08);
    border: 1px solid rgba(255, 99, 99, 0.2);
    padding: 3px 10px;
    border-radius: 9999px;
    margin-bottom: 12px;
    letter-spacing: 0.8px;
  }

  .pulse-dot {
    width: 6px;
    height: 6px;
    background: var(--color-coral);
    border-radius: 50%;
    box-shadow: 0 0 8px var(--color-coral);
  }

  .wizard-container {
    padding: 0;
    overflow: hidden;
    background: #060709;
    border: 1px solid var(--color-border);
    border-radius: 12px;
  }

  /* Tabs */
  .tabs-scroll-area {
    width: 100%;
    overflow-x: auto;
    background: #030405;
    border-bottom: 1px solid var(--color-border);
    scrollbar-width: thin;
    scrollbar-color: #1f2127 transparent;
  }

  .tabs-scroll-area::-webkit-scrollbar {
    height: 4px;
  }

  .tabs-scroll-area::-webkit-scrollbar-thumb {
    background: #1f2127;
    border-radius: 2px;
  }

  .platform-tabs {
    display: flex;
    align-items: stretch;
    padding: 6px;
    gap: 4px;
    min-width: max-content;
  }

  .tab-btn {
    display: flex;
    align-items: center;
    gap: 8px;
    background: transparent;
    border: 1px solid transparent;
    border-radius: 8px;
    padding: 8px 14px;
    color: var(--color-ash);
    cursor: pointer;
    font-size: 13px;
    font-weight: 500;
    transition: all 0.15s ease;
    white-space: nowrap;
  }

  .tab-btn:hover {
    color: var(--color-pure-white);
    background: rgba(255, 255, 255, 0.04);
  }

  .tab-btn.active {
    background: #0d0e12;
    color: var(--color-pure-white);
    border-color: rgba(255, 255, 255, 0.1);
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.4);
  }

  .tab-badge {
    font-size: 9.5px;
    padding: 2px 6px;
    border-radius: 4px;
    background: rgba(255, 255, 255, 0.06);
    color: #8e9099;
    letter-spacing: 0.4px;
  }

  .tab-btn.active .tab-badge {
    background: rgba(255, 99, 99, 0.15);
    color: var(--color-coral);
    border: 1px solid rgba(255, 99, 99, 0.25);
  }

  /* Panel */
  .wizard-panel {
    padding: 24px;
    display: flex;
    flex-direction: column;
    gap: 20px;
  }

  .panel-header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 16px;
    flex-wrap: wrap;
  }

  .panel-info {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  .panel-title-row {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .panel-title {
    font-size: 20px;
    font-weight: 500;
    color: var(--color-pure-white);
    margin: 0;
  }

  .panel-subtitle {
    font-size: 13px;
    color: var(--color-ash);
    margin: 0;
  }

  .install-btn {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 8px 14px;
    font-size: 12px;
    min-height: 36px;
    white-space: nowrap;
  }

  /* Quick Command Bar */
  .quick-cmd-bar {
    display: flex;
    align-items: center;
    gap: 12px;
    background: #030405;
    border: 1px solid var(--color-border);
    border-radius: 8px;
    padding: 10px 14px;
    overflow-x: auto;
    max-width: 100%;
    min-width: 0;
  }

  .cmd-label {
    font-size: 10px;
    font-weight: 600;
    letter-spacing: 0.6px;
    color: var(--color-coral);
    background: rgba(255, 99, 99, 0.1);
    padding: 2px 6px;
    border-radius: 4px;
    flex-shrink: 0;
  }

  .cmd-code {
    font-size: 13px;
    color: var(--color-pure-white);
    white-space: nowrap;
    overflow-x: auto;
  }

  /* File Box */
  .file-box {
    background: #030405;
    border: 1px solid var(--color-border);
    border-radius: 8px;
    overflow: hidden;
  }

  .file-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
    padding: 8px 14px;
    background: #08090d;
    border-bottom: 1px solid var(--color-border);
  }

  .file-title-group {
    display: flex;
    align-items: center;
    gap: 8px;
    color: var(--color-ash);
    min-width: 0;
  }

  .file-path {
    font-size: 12px;
    color: var(--color-pure-white);
    font-weight: 500;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .copy-file-btn {
    background: transparent;
    border: 1px solid var(--color-border);
    border-radius: 6px;
    color: var(--color-ash);
    font-size: 11px;
    padding: 4px 10px;
    cursor: pointer;
    transition: all 0.15s ease;
    white-space: nowrap;
    flex-shrink: 0;
  }

  .copy-file-btn:hover {
    color: var(--color-pure-white);
    border-color: rgba(255, 255, 255, 0.2);
    background: rgba(255, 255, 255, 0.05);
  }

  .file-code {
    margin: 0;
    padding: 14px;
    font-size: 12.5px;
    line-height: 1.6;
    color: #d1d5db;
    overflow-x: auto;
    max-height: 260px;
    max-width: 100%;
    box-sizing: border-box;
  }

  /* Meta Cards Grid */
  .meta-cards-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
  }

  .meta-card {
    background: rgba(13, 14, 18, 0.6);
    border: 1px solid var(--color-border);
    border-radius: 8px;
    padding: 14px;
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  .card-label {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 10px;
    letter-spacing: 0.6px;
    color: var(--color-ash);
    font-weight: 600;
  }

  .card-text {
    font-size: 12.5px;
    color: #c9cdd4;
    line-height: 1.5;
    margin: 0;
  }

  @media (max-width: 768px) {
    .wizard-panel {
      padding: 16px;
      gap: 16px;
    }

    .meta-cards-grid {
      grid-template-columns: 1fr;
    }

    .panel-header {
      flex-direction: column;
      align-items: flex-start;
    }

    .install-btn {
      width: 100%;
      justify-content: center;
    }

    .file-header {
      flex-direction: column;
      align-items: flex-start;
      gap: 8px;
    }

    .copy-file-btn {
      width: 100%;
      text-align: center;
    }
  }
</style>
