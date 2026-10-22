<script lang="ts">
  type Category = 'all' | 'communication' | 'lean' | 'verification' | 'hygiene' | 'safety';

  let selectedCategory = $state<Category>('all');
  let searchQuery = $state('');
  let expandedRule = $state<number | null>(1); // Expand Rule 1 by default
  let copiedRule = $state<number | null>(null);

  interface Rule {
    id: number;
    title: string;
    category: Category;
    categoryName: string;
    summary: string;
    directive: string;
  }

  const rules: Rule[] = [
    {
      id: 1,
      title: 'Answer-First & Zero Ceremony',
      category: 'communication',
      categoryName: 'Communication',
      summary: 'Line 1 must be the direct technical answer, command, or code diff. Prohibit conversational openers ("Sure!") and closers ("Hope this helps!").',
      directive: 'Line 1 must be the direct technical solution, diff, or terminal command. Zero pleasantries, conversational preambles, or closing ceremony in any language.'
    },
    {
      id: 2,
      title: 'Numbered Actionable Steps',
      category: 'communication',
      categoryName: 'Communication',
      summary: 'Multi-step work must always be a numbered list (1 step = 1 bounded action). Use only as many steps as necessary.',
      directive: 'Present multi-step instructions as a numbered list (1 step = 1 bounded action). Use only necessary steps; do not pad steps.'
    },
    {
      id: 3,
      title: 'Progressive Disclosure (Cognitive Cap)',
      category: 'communication',
      categoryName: 'Communication',
      summary: 'Cap routine responses to top 5 items per list without concealing security, data integrity, or breaking changes.',
      directive: 'Prioritize top 5 items for routine responses. Never conceal material findings affecting security, data integrity, or breaking changes.'
    },
    {
      id: 4,
      title: 'Useful Time Estimates',
      category: 'communication',
      categoryName: 'Communication',
      summary: 'State measured duration estimates (~2 minutes, ~15 minutes) when context permits, with clear assumptions.',
      directive: 'Provide concrete duration estimates (~2 minutes, ~15 minutes) when context permits. State assumptions; avoid arbitrary guesses.'
    },
    {
      id: 5,
      title: 'Actionable Ending',
      category: 'communication',
      categoryName: 'Communication',
      summary: 'Conclude with at most 1 concrete next step when action is needed. Stop cleanly when complete.',
      directive: 'Conclude with at most 1 concrete next step when further action is needed to advance the task. Stop cleanly when complete.'
    },
    {
      id: 6,
      title: 'The Simplicity Ladder',
      category: 'lean',
      categoryName: 'Lean Architecture',
      summary: 'Reject speculation (YAGNI) → Reuse local code → Standard library first → Native platform features → Smallest diff at root cause.',
      directive: 'Apply the Simplicity Ladder: Reject speculation (YAGNI) -> Reuse local code -> Standard library first -> Native platform features -> Smallest diff at root cause.'
    },
    {
      id: 7,
      title: 'Reject Speculative Abstractions',
      category: 'lean',
      categoryName: 'Lean Architecture',
      summary: 'Prohibit single-implementor interfaces, config files for invariant values, and wrapper classes when direct calls suffice.',
      directive: 'No single-implementation interfaces. No configuration files for invariant values. No factories or wrappers when direct calls suffice.'
    },
    {
      id: 8,
      title: 'Investigate Before Editing',
      category: 'lean',
      categoryName: 'Lean Architecture',
      summary: 'Trace callers, dependencies, and side effects proportionately before editing shared code, APIs, or state.',
      directive: 'Trace callers, dependencies, and side effects proportionately before editing (deeper for auth, public APIs, schema mutations).'
    },
    {
      id: 9,
      title: 'Risk-Based Clarification',
      category: 'lean',
      categoryName: 'Lean Architecture',
      summary: 'Stop guessing on material ambiguities. Present up to 2-3 options with 1-line trade-offs each and state recommendation.',
      directive: 'Ask when ambiguity materially affects correctness, security, or architecture; make reversible assumptions for low-risk details.'
    },
    {
      id: 10,
      title: 'Risk-Proportionate Verification',
      category: 'verification',
      categoryName: 'Verification & Contracts',
      summary: 'Every non-trivial modification requires runnable verification. Never claim a test passed without execution.',
      directive: 'Include runnable verification for non-trivial modifications (branching, loops, state mutations, parsers, finance, auth, shared interfaces). Disclose unverified status if not run.'
    },
    {
      id: 11,
      title: 'Preserve Existing Contracts',
      category: 'verification',
      categoryName: 'Verification & Contracts',
      summary: 'Protect public interfaces, schemas, and shared types. Default to backward-compatible changes; flag breaking changes explicitly.',
      directive: 'Protect public interfaces, schemas, and shared types. Default to backward-compatible changes; flag breaking changes explicitly.'
    },
    {
      id: 12,
      title: 'Dependency Discipline',
      category: 'verification',
      categoryName: 'Verification & Contracts',
      summary: 'Standard library first. Add third-party packages only when they materially improve security, correctness, or maintainability.',
      directive: 'Standard library and native platform features first. Add dependencies only when they materially improve security, correctness, or maintainability.'
    },
    {
      id: 13,
      title: 'Remove Noise Comments',
      category: 'hygiene',
      categoryName: 'Code Hygiene',
      summary: 'Delete code-restating comments (// init count), decorative ASCII banners (// =======), and decorative emoji.',
      directive: 'Delete code-restating comments, decorative banner dividers, and emoji in comments and code. Do not write vague TODOs lacking actionable context.'
    },
    {
      id: 14,
      title: 'Preserve High-Value Comments',
      category: 'hygiene',
      categoryName: 'Code Hygiene',
      summary: 'Retain comments explaining non-obvious business logic, security boundaries, and upstream bug workarounds (WHY, not WHAT).',
      directive: 'Preserve only WHY comments explaining non-obvious business logic, security constraints, and upstream bug workarounds.'
    },
    {
      id: 15,
      title: 'Destructive Action Safeguard',
      category: 'safety',
      categoryName: 'Safety Gates',
      summary: 'Halt for explicit user confirmation before executing irreversible commands (rm -rf, git reset --hard, DROP TABLE, terraform destroy).',
      directive: 'Inspect scope and request explicit confirmation before executing irreversible commands, unconstrained DB mutations, infrastructure teardown, or remote production changes.'
    },
    {
      id: 16,
      title: 'Non-Negotiable Security Boundaries',
      category: 'safety',
      categoryName: 'Safety Gates',
      summary: 'Never simplify away input validation, error handling, authentication checks, least-privilege boundaries, or expose secrets.',
      directive: 'Never simplify away input validation, error handling, auth checks, or least-privilege boundaries. Never expose credentials or secrets in logs or responses.'
    }
  ];

  let filteredRules = $derived.by(() => {
    return rules.filter(r => {
      const matchCategory = selectedCategory === 'all' || r.category === selectedCategory;
      const matchSearch = searchQuery.trim() === '' ||
        r.title.toLowerCase().includes(searchQuery.toLowerCase()) ||
        r.summary.toLowerCase().includes(searchQuery.toLowerCase()) ||
        r.id.toString() === searchQuery.trim();
      return matchCategory && matchSearch;
    });
  });

  function toggleRule(id: number) {
    expandedRule = expandedRule === id ? null : id;
  }

  function copyDirective(rule: Rule, e: MouseEvent) {
    e.stopPropagation();
    navigator.clipboard.writeText(`Rule ${rule.id} (${rule.title}): ${rule.directive}`);
    copiedRule = rule.id;
    setTimeout(() => { copiedRule = null; }, 2000);
  }
</script>

<section id="rules" class="rules-section container">
  <!-- Section Header -->
  <div class="section-header">
    <span class="badge-tag">ENGINEERING PROTOCOL</span>
    <h2 class="section-title">The 16 Rules of Gluttony</h2>
    <p class="section-desc">
      A canonical, prioritized directive set enforcing lean execution, safety gates, and zero ceremony across every agent session.
    </p>
  </div>

  <!-- Filter & Search Bar -->
  <div class="rules-filter-bar key-card">
    <!-- Category Pills -->
    <div class="category-pills">
      <button
        class="cat-pill font-mono"
        class:active={selectedCategory === 'all'}
        onclick={() => selectedCategory = 'all'}
      >
        All (16)
      </button>
      <button
        class="cat-pill font-mono"
        class:active={selectedCategory === 'communication'}
        onclick={() => selectedCategory = 'communication'}
      >
        Communication
      </button>
      <button
        class="cat-pill font-mono"
        class:active={selectedCategory === 'lean'}
        onclick={() => selectedCategory = 'lean'}
      >
        Lean Architecture
      </button>
      <button
        class="cat-pill font-mono"
        class:active={selectedCategory === 'verification'}
        onclick={() => selectedCategory = 'verification'}
      >
        Verification
      </button>
      <button
        class="cat-pill font-mono"
        class:active={selectedCategory === 'hygiene'}
        onclick={() => selectedCategory = 'hygiene'}
      >
        Hygiene
      </button>
      <button
        class="cat-pill font-mono"
        class:active={selectedCategory === 'safety'}
        onclick={() => selectedCategory = 'safety'}
      >
        Safety Gates
      </button>
    </div>

    <!-- Search input -->
    <div class="search-input-wrap">
      <svg class="search-icon" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <circle cx="11" cy="11" r="8"></circle>
        <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
      </svg>
      <input
        type="text"
        bind:value={searchQuery}
        placeholder="Filter rules by keyword..."
        class="recessed-well rule-search-input font-mono"
      />
    </div>
  </div>

  <!-- Rules Accordion List -->
  <div class="rules-list">
    {#if filteredRules.length === 0}
      <div class="empty-state key-card">
        <span class="font-mono text-smoke">No matching rules found for "{searchQuery}".</span>
      </div>
    {:else}
      {#each filteredRules as rule}
        <div
          class="rule-card key-card"
          class:expanded={expandedRule === rule.id}
          onclick={() => toggleRule(rule.id)}
          role="button"
          tabindex="0"
          onkeydown={(e) => e.key === 'Enter' && toggleRule(rule.id)}
        >
          <!-- Rule Header Row -->
          <div class="rule-row">
            <div class="rule-left">
              <span class="rule-number font-mono text-coral">RULE {rule.id.toString().padStart(2, '0')}</span>
              <h3 class="rule-title">{rule.title}</h3>
              <span class="badge-tag rule-badge">{rule.categoryName}</span>
            </div>

            <div class="rule-right">
              <button
                onclick={(e) => copyDirective(rule, e)}
                class="btn-ghost rule-copy-btn font-mono"
                title="Copy directive prompt snippet"
              >
                {copiedRule === rule.id ? 'Copied' : 'Copy'}
              </button>
              <span class="chevron-indicator" class:open={expandedRule === rule.id}>▾</span>
            </div>
          </div>

          <!-- Summary preview -->
          <p class="rule-summary text-ash">{rule.summary}</p>

          <!-- Expanded Directive Box -->
          {#if expandedRule === rule.id}
            <div class="directive-box">
              <span class="directive-label font-mono">CANONICAL PROMPT DIRECTIVE:</span>
              <pre class="directive-code font-mono"><code>{rule.directive}</code></pre>
            </div>
          {/if}
        </div>
      {/each}
    {/if}
  </div>
</section>

<style>
  .rules-section {
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
    max-width: 580px;
  }

  .rules-filter-bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 18px;
    margin-bottom: 24px;
    gap: 16px;
    flex-wrap: wrap;
    background-color: var(--color-ink);
  }

  .category-pills {
    display: flex;
    align-items: center;
    gap: 6px;
    overflow-x: auto;
    scrollbar-width: none;
    max-width: 100%;
  }

  .category-pills::-webkit-scrollbar {
    display: none;
  }

  .cat-pill {
    background: transparent;
    border: 1px solid transparent;
    color: var(--color-ash);
    font-size: 11px;
    padding: 5px 9px;
    border-radius: 6px;
    cursor: pointer;
    white-space: nowrap;
    transition: all 0.15s ease;
  }

  .cat-pill:hover {
    color: var(--color-pure-white);
    background-color: rgba(255, 255, 255, 0.05);
  }

  .cat-pill.active {
    background-color: #1b1c1e;
    color: var(--color-pure-white);
    border-color: rgba(255, 255, 255, 0.2);
    box-shadow: var(--shadow-key-subtle);
  }

  .search-input-wrap {
    display: flex;
    align-items: center;
    gap: 8px;
    flex-grow: 1;
    max-width: 280px;
    min-width: 180px;
  }

  .search-icon {
    color: var(--color-smoke);
    flex-shrink: 0;
  }

  .rule-search-input {
    width: 100%;
    font-size: 12px;
    padding: 6px 10px;
  }

  .rules-list {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .rule-card {
    padding: 18px 20px;
    cursor: pointer;
    display: flex;
    flex-direction: column;
    gap: 10px;
    transition: border-color 0.15s ease, background-color 0.15s ease;
  }

  .rule-card:hover {
    border-color: rgba(255, 255, 255, 0.25);
  }

  .rule-card.expanded {
    border-color: rgba(255, 99, 99, 0.35);
    background-color: #080a0d;
  }

  .rule-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
  }

  .rule-left {
    display: flex;
    align-items: center;
    gap: 10px;
    flex-wrap: wrap;
  }

  .rule-number {
    font-size: 11px;
    letter-spacing: 0.8px;
    font-weight: 600;
  }

  .rule-title {
    font-size: 15px;
    font-weight: 500;
    color: var(--color-pure-white);
  }

  .rule-badge {
    font-size: 9.5px;
  }

  .rule-right {
    display: flex;
    align-items: center;
    gap: 10px;
    flex-shrink: 0;
  }

  .rule-copy-btn {
    padding: 4px 10px;
    font-size: 10.5px;
    min-height: 28px;
  }

  .chevron-indicator {
    color: var(--color-smoke);
    font-size: 14px;
    transition: transform 0.2s ease;
  }

  .chevron-indicator.open {
    transform: rotate(180deg);
    color: var(--color-coral-pulse);
  }

  .rule-summary {
    font-size: 13.5px;
    line-height: 1.5;
  }

  .directive-box {
    margin-top: 6px;
    padding: 12px 14px;
    background-color: var(--color-obsidian);
    border: 1px solid rgba(255, 255, 255, 0.08);
    border-radius: var(--radius-inputs);
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  .directive-label {
    font-size: 10px;
    color: var(--color-smoke);
    letter-spacing: 0.6px;
  }

  .directive-code {
    font-size: 12px;
    color: var(--color-mist);
    white-space: pre-wrap;
    word-break: break-word;
    line-height: 1.5;
  }

  .empty-state {
    padding: 32px;
    text-align: center;
  }

  @media (max-width: 640px) {
    .rules-filter-bar {
      flex-direction: column;
      align-items: stretch;
    }

    .search-input-wrap {
      max-width: 100%;
    }

    .rule-title {
      font-size: 14px;
    }

    .rule-summary {
      font-size: 12.5px;
    }
  }
</style>
