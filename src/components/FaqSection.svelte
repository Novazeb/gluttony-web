<script lang="ts">
  type FaqItem = {
    id: string;
    question: string;
    category: 'Architecture' | 'Workflow' | 'Security';
    answer: string;
    takeaway: string;
  };

  const faqList: FaqItem[] = [
    {
      id: 'faq-intelligence',
      question: 'Does Gluttony reduce LLM reasoning or starve model context?',
      category: 'Architecture',
      answer: 'No. Gluttony strips decorative verbosity, rhetorical cheerleading, and speculative abstractions, which actually preserves the attention window for real AST parsing and architectural contracts. By keeping prompts and diffs lean, token entropy drops by 38.6%, significantly reducing hallucination and context drift.',
      takeaway: 'Answer-First + YAGNI focuses LLM attention on root causes, not boilerplate.',
    },
    {
      id: 'faq-escalation',
      question: 'How does the automatic escalation to [strict] mode trigger?',
      category: 'Security',
      answer: 'Gluttony monitors file paths and code boundaries. When an edit touches authentication handlers, payment gateways, database migrations, cryptographic functions, or public schema contracts, the agent automatically steps up from [full] to [strict]. It explicitly discloses `[strict: security/contract boundary touched]` and enforces rigorous bracketed verification.',
      takeaway: 'Auto-escalation prevents accidental security holes without human intervention.',
    },
    {
      id: 'faq-models',
      question: 'Which LLMs and agent harnesses are compatible?',
      category: 'Architecture',
      answer: 'Gluttony is model-agnostic and tested against Claude 3.5/3.7 Sonnet, OpenAI GPT-4o & o3-mini, Google Gemini 2.0 Flash & Pro, and DeepSeek-R1. It compiles into native formats for Claude Code (CLAUDE.md), Cursor (.cursor/rules), Google Antigravity (GEMINI.md), Windsurf, Cline (.clinerules), and universal AGENTS.md.',
      takeaway: 'One universal Single Source of Truth compiles to 14+ AI developer tools.',
    },
    {
      id: 'faq-stdlib',
      question: 'Why does Gluttony mandate standard library before adding dependencies?',
      category: 'Architecture',
      answer: 'Third-party dependencies introduce supply chain attack vectors, licensing risks, breaking update churn, and bloated container images. Gluttony follows the Simplicity Ladder: YAGNI -> Local Code -> Standard Library -> Platform Native -> External Package. Packages are allowed only when they materially improve security or maintainability.',
      takeaway: 'Zero unvetted micro-packages, zero supply chain surprises.',
    },
    {
      id: 'faq-privacy',
      question: 'Is any proprietary codebase code or telemetry sent to external servers?',
      category: 'Security',
      answer: 'Zero bytes. Gluttony is 100% local markdown rules, TOML specs, and shell scripts executed strictly on your machine. There are no tracking beacons, no analytics endpoints, no middleman proxies, and no external API dependencies. Your IP and source code stay completely in your private environment.',
      takeaway: '100% offline, private, and air-gapped safe.',
    },
    {
      id: 'faq-prompt-vs-rules',
      question: 'How is Gluttony different from adding loose custom prompt instructions?',
      category: 'Workflow',
      answer: 'Loose prompt instructions are easily ignored by LLMs as context grows. Gluttony uses a deterministic 16-rule state protocol with explicit operational modes (lite, full, strict), destructive action safety gates (confirming rm -rf, drop table, force push), and compile-time verification tests that validate the agent adheres to constraints.',
      takeaway: 'Enforced operational safety gates and verifiable bracketed changes.',
    },
  ];

  let openId = $state<string | null>('faq-intelligence');
  let selectedCategory = $state<'ALL' | 'Architecture' | 'Workflow' | 'Security'>('ALL');
  let searchQuery = $state('');

  let filteredFaqs = $derived.by(() => {
    return faqList.filter((f) => {
      const matchesCat = selectedCategory === 'ALL' || f.category === selectedCategory;
      const q = searchQuery.trim().toLowerCase();
      const matchesSearch = !q || f.question.toLowerCase().includes(q) || f.answer.toLowerCase().includes(q) || f.takeaway.toLowerCase().includes(q);
      return matchesCat && matchesSearch;
    });
  });

  function toggleFaq(id: string) {
    openId = openId === id ? null : id;
  }
</script>

<section id="faq" class="faq-section container">
  <!-- Section Header -->
  <div class="section-header">
    <div class="header-eyebrow font-mono">
      <span class="pulse-dot"></span>
      <span>TECHNICAL FAQ</span>
    </div>
    <h2 class="section-title">Frequently Asked Questions</h2>
    <p class="section-desc">
      Architectural decisions, runtime behavior, and safety mechanisms behind Gluttony.
    </p>
  </div>

  <!-- Search & Filter Controls -->
  <div class="faq-controls">
    <div class="search-box">
      <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <circle cx="11" cy="11" r="8"></circle>
        <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
      </svg>
      <input
        type="text"
        bind:value={searchQuery}
        placeholder="Filter questions by keyword (e.g. telemetry, strict, models)..."
        class="search-input font-mono"
      />
    </div>

    <div class="category-pills">
      {#each ['ALL', 'Architecture', 'Workflow', 'Security'] as cat}
        <button
          class="cat-pill font-mono"
          class:active={selectedCategory === cat}
          onclick={() => (selectedCategory = cat as any)}
        >
          {cat}
        </button>
      {/each}
    </div>
  </div>

  <!-- FAQ Accordion List -->
  <div class="faq-list">
    {#each filteredFaqs as faq}
      {@const isOpen = openId === faq.id}
      <div class="faq-card key-card" class:open={isOpen}>
        <button
          class="faq-header"
          onclick={() => toggleFaq(faq.id)}
          aria-expanded={isOpen}
          aria-controls={faq.id}
        >
          <div class="faq-title-group">
            <span class="faq-badge font-mono">{faq.category}</span>
            <span class="faq-question">{faq.question}</span>
          </div>

          <div class="toggle-indicator">
            <svg
              width="14"
              height="14"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2.5"
              class="chevron-icon"
              class:rotated={isOpen}
            >
              <polyline points="6 9 12 15 18 9"></polyline>
            </svg>
          </div>
        </button>

        {#if isOpen}
          <div id={faq.id} class="faq-body">
            <p class="faq-answer">{faq.answer}</p>
            <div class="faq-takeaway">
              <span class="takeaway-label font-mono">RULE TAKEAWAY:</span>
              <span class="takeaway-text font-mono">{faq.takeaway}</span>
            </div>
          </div>
        {/if}
      </div>
    {/each}

    {#if filteredFaqs.length === 0}
      <div class="no-results font-mono">
        <span>No matching questions found for "{searchQuery}".</span>
      </div>
    {/if}
  </div>
</section>

<style>
  .faq-section {
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

  /* Controls */
  .faq-controls {
    display: flex;
    flex-direction: column;
    gap: 12px;
    margin-bottom: 24px;
  }

  .search-box {
    display: flex;
    align-items: center;
    gap: 10px;
    background: #060709;
    border: 1px solid var(--color-border);
    border-radius: 8px;
    padding: 10px 14px;
    color: var(--color-ash);
  }

  .search-input {
    background: transparent;
    border: none;
    outline: none;
    color: var(--color-pure-white);
    font-size: 13px;
    width: 100%;
  }

  .search-input::placeholder {
    color: #555761;
  }

  .category-pills {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
  }

  .cat-pill {
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid var(--color-border);
    border-radius: 6px;
    padding: 4px 12px;
    color: var(--color-ash);
    font-size: 11px;
    cursor: pointer;
    transition: all 0.15s ease;
  }

  .cat-pill:hover {
    color: var(--color-pure-white);
    background: rgba(255, 255, 255, 0.07);
  }

  .cat-pill.active {
    background: rgba(255, 99, 99, 0.12);
    color: var(--color-coral);
    border-color: rgba(255, 99, 99, 0.3);
  }

  /* List */
  .faq-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .faq-card {
    background: #060709;
    border: 1px solid var(--color-border);
    border-radius: 10px;
    overflow: hidden;
    padding: 0;
    transition: border-color 0.2s ease, box-shadow 0.2s ease;
  }

  .faq-card.open {
    border-color: rgba(255, 255, 255, 0.15);
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.4);
  }

  .faq-header {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
    background: transparent;
    border: none;
    padding: 16px 20px;
    text-align: left;
    cursor: pointer;
    color: var(--color-pure-white);
  }

  .faq-title-group {
    display: flex;
    align-items: center;
    gap: 12px;
    flex-wrap: wrap;
    min-width: 0;
  }

  .faq-badge {
    font-size: 9.5px;
    font-weight: 600;
    letter-spacing: 0.5px;
    padding: 2px 7px;
    border-radius: 4px;
    background: rgba(255, 255, 255, 0.06);
    color: #9da0aa;
    text-transform: uppercase;
  }

  .faq-question {
    font-size: 14.5px;
    font-weight: 500;
    color: #e5e7eb;
    line-height: 1.4;
  }

  .toggle-indicator {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 28px;
    height: 28px;
    border-radius: 6px;
    background: rgba(255, 255, 255, 0.04);
    color: var(--color-ash);
    flex-shrink: 0;
  }

  .chevron-icon {
    transition: transform 0.2s ease;
  }

  .chevron-icon.rotated {
    transform: rotate(180deg);
  }

  .faq-body {
    padding: 0 20px 20px 20px;
    display: flex;
    flex-direction: column;
    gap: 14px;
    border-top: 1px solid rgba(255, 255, 255, 0.04);
    padding-top: 16px;
  }

  .faq-answer {
    font-size: 13.5px;
    line-height: 1.6;
    color: #abb0ba;
    margin: 0;
  }

  .faq-takeaway {
    display: flex;
    align-items: flex-start;
    gap: 8px;
    background: #020304;
    border: 1px solid rgba(255, 99, 99, 0.15);
    border-left: 3px solid var(--color-coral);
    border-radius: 6px;
    padding: 10px 14px;
    font-size: 12px;
  }

  .takeaway-label {
    color: var(--color-coral);
    font-weight: 600;
    flex-shrink: 0;
  }

  .takeaway-text {
    color: #d1d5db;
  }

  .no-results {
    padding: 30px;
    text-align: center;
    color: var(--color-ash);
    font-size: 13px;
    background: #060709;
    border: 1px dashed var(--color-border);
    border-radius: 8px;
  }

  @media (max-width: 768px) {
    .faq-header {
      padding: 14px;
      gap: 12px;
    }

    .faq-question {
      font-size: 13.5px;
    }

    .faq-body {
      padding: 0 14px 16px 14px;
      padding-top: 12px;
    }

    .faq-takeaway {
      flex-direction: column;
      gap: 4px;
    }
  }
</style>
