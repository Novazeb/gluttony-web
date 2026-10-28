<script lang="ts">
  type ScenarioKey = 'pagination' | 'ratelimit' | 'dto';

  let activeScenario = $state<ScenarioKey>('pagination');
  let viewMode = $state<'split' | 'gluttony' | 'raw'>('split');
  let copied = $state(false);

  const scenarios: Record<ScenarioKey, {
    title: string;
    subtitle: string;
    badge: string;
    rawLines: number;
    gluttonyLines: number;
    diffPruned: string;
    rawCode: string;
    gluttonyCode: string;
    explanation: string;
  }> = {
    pagination: {
      title: 'Pagination Offset Calculation',
      subtitle: 'Bug fix in API endpoint pagination',
      badge: 'ROOT CAUSE FIX',
      rawLines: 28,
      gluttonyLines: 4,
      diffPruned: '-85% code bloat',
      explanation: 'Raw AI speculatively created a singleton PaginationService class with lodash import. Gluttony fixed the offset boundary math in 4 clean lines directly at the root cause.',
      rawCode: `// Import lodash helper for pagination chunking
import _ from 'lodash';

// Pagination service wrapper interface
export interface IPaginationService {
  calculateOffset(page: number, limit: number): number;
  paginateResults<T>(items: T[], page: number, limit: number): T[];
}

// Concrete pagination service implementation
export class PaginationService implements IPaginationService {
  private defaultLimit: number = 20;

  // Initialize offset calculation
  public calculateOffset(page: number, limit?: number): number {
    const pageNum = page && page > 0 ? page : 1;
    const limitNum = limit || this.defaultLimit;
    // Calculate total offset
    return (pageNum - 1) * limitNum;
  }

  // Paginate given item array using lodash
  public paginateResults<T>(items: T[], page: number, limit: number): T[] {
    const chunks = _.chunk(items, limit);
    return chunks[page - 1] || [];
  }
}`,
      gluttonyCode: `// Root cause: off-by-one zero-index boundary
export function getPageSlice<T>(items: T[], page = 1, limit = 20): T[] {
  const start = Math.max(0, (page - 1) * limit);
  return items.slice(start, start + limit);
}`
    },
    ratelimit: {
      title: 'In-Memory Request Rate Limiter',
      subtitle: 'Feature addition for API abuse prevention',
      badge: 'STDLIB FIRST',
      rawLines: 36,
      gluttonyLines: 12,
      diffPruned: 'Zero 3rd-party dependencies',
      explanation: 'Raw AI installed express-rate-limit and redis packages. Gluttony implemented an in-memory sliding window using native standard library Map and Date.now().',
      rawCode: `import rateLimit from 'express-rate-limit';
import RedisStore from 'rate-limit-redis';
import Redis from 'ioredis';

// Speculative Redis client for single-node process
const client = new Redis({
  host: process.env.REDIS_HOST || '127.0.0.1',
  port: Number(process.env.REDIS_PORT) || 6379,
  lazyConnect: true
});

// Configure distributed limiter factory
export function createRateLimiter(windowMs = 60000, max = 100) {
  return rateLimit({
    windowMs,
    max,
    standardHeaders: true,
    legacyHeaders: false,
    store: new RedisStore({
      // @ts-ignore sendCommand
      sendCommand: (...args: string[]) => client.call(...args),
    }),
    handler: (req, res) => {
      res.status(429).json({ error: 'Too many requests' });
    }
  });
}`,
      gluttonyCode: `// Native stdlib Map limiter: zero external dependencies
const hits = new Map<string, { count: number; resetAt: number }>();

export function isRateLimited(key: string, limit = 100, windowMs = 60000): boolean {
  const now = Date.now();
  const entry = hits.get(key);
  if (!entry || now > entry.resetAt) {
    hits.set(key, { count: 1, resetAt: now + windowMs });
    return false;
  }
  return ++entry.count > limit;
}`
    },
    dto: {
      title: 'User Profile DTO Migration',
      subtitle: 'Schema evolution with consumer preservation',
      badge: 'CONTRACT INTEGRITY',
      rawLines: 24,
      gluttonyLines: 10,
      diffPruned: '100% backward compatible',
      explanation: 'Raw AI renamed fullName to name without alias, breaking 4 downstream microservices. Gluttony preserved the existing API schema contract via a getter alias.',
      rawCode: `// Breaking schema migration
export interface UserDTO {
  id: string;
  // Breaking change: removed fullName, added name
  name: string;
  email: string;
}

export function transformUser(raw: any): UserDTO {
  return {
    id: raw.id,
    // Downstream services querying .fullName now fail with undefined
    name: raw.first_name + ' ' + raw.last_name,
    email: raw.email
  };
}`,
      gluttonyCode: `export class UserDTO {
  constructor(
    public readonly id: string,
    public readonly name: string,
    public readonly email: string
  ) {}

  // Rule 11: Preserve existing contract for legacy callers
  get fullName(): string { return this.name; }
}`
    }
  };

  function copyGluttonyCode() {
    navigator.clipboard.writeText(scenarios[activeScenario].gluttonyCode);
    copied = true;
    setTimeout(() => { copied = false; }, 2000);
  }
</script>

<section id="diff" class="diff-section container">
  <!-- Section Header -->
  <div class="section-header">
    <span class="badge-tag">EMPIRICAL CODE COMPARISON</span>
    <h2 class="section-title">Raw AI Slop vs Gluttony Diff</h2>
    <p class="section-desc">
      See the difference in code footprint, cognitive load, and architectural restraint across real development tasks.
    </p>
  </div>

  <!-- Main Diff Card -->
  <div class="diff-card key-card">
    <!-- Card Toolbar -->
    <div class="diff-toolbar">
      <!-- Scenario Selector Tabs -->
      <div class="scenario-tabs">
        <button
          class="scenario-btn font-mono"
          class:active={activeScenario === 'pagination'}
          onclick={() => activeScenario = 'pagination'}
        >
          1. Bug Fix
        </button>
        <button
          class="scenario-btn font-mono"
          class:active={activeScenario === 'ratelimit'}
          onclick={() => activeScenario = 'ratelimit'}
        >
          2. Rate Limiter
        </button>
        <button
          class="scenario-btn font-mono"
          class:active={activeScenario === 'dto'}
          onclick={() => activeScenario = 'dto'}
        >
          3. DTO Contract
        </button>
      </div>

      <!-- View Switcher -->
      <div class="view-switcher">
        <button
          class="view-btn font-mono"
          class:active={viewMode === 'split'}
          onclick={() => viewMode = 'split'}
        >
          Split
        </button>
        <button
          class="view-btn font-mono"
          class:active={viewMode === 'gluttony'}
          onclick={() => viewMode = 'gluttony'}
        >
          Gluttony
        </button>
        <button
          class="view-btn font-mono"
          class:active={viewMode === 'raw'}
          onclick={() => viewMode = 'raw'}
        >
          Raw AI
        </button>
      </div>
    </div>

    <!-- Impact Bar -->
    <div class="impact-bar font-mono">
      <div class="impact-left">
        <span class="badge-tag">{scenarios[activeScenario].badge}</span>
        <span class="scenario-name text-pure-white">{scenarios[activeScenario].title}</span>
      </div>
      <div class="impact-right">
        <span class="stat-tag text-coral">{scenarios[activeScenario].diffPruned}</span>
        <button onclick={copyGluttonyCode} class="btn-ghost copy-btn">
          {copied ? 'Copied Lean Code' : 'Copy Lean Code'}
        </button>
      </div>
    </div>

    <!-- Code Display Area -->
    <div class="code-panels" class:split-view={viewMode === 'split'}>
      <!-- Raw AI Panel -->
      {#if viewMode === 'split' || viewMode === 'raw'}
        <div class="code-panel panel-raw">
          <div class="panel-header font-mono">
            <span class="panel-label text-smoke">RAW AI BASELINE ({scenarios[activeScenario].rawLines} LINES)</span>
            <span class="verdict-bad">AI BOILERPLATE & WRAPPERS</span>
          </div>
          <div class="diff-code-table font-mono">
            {#each scenarios[activeScenario].rawCode.split('\n') as line, idx}
              <div class="diff-line diff-del">
                <span class="line-num">{idx + 1}</span>
                <span class="diff-marker">−</span>
                <span class="line-content">{line}</span>
              </div>
            {/each}
          </div>
        </div>
      {/if}

      <!-- Gluttony Panel -->
      {#if viewMode === 'split' || viewMode === 'gluttony'}
        <div class="code-panel panel-gluttony">
          <div class="panel-header font-mono">
            <span class="panel-label text-coral">GLUTTONY MODE ({scenarios[activeScenario].gluttonyLines} LINES)</span>
            <span class="verdict-good">SURGICAL ROOT CAUSE</span>
          </div>
          <div class="diff-code-table font-mono">
            {#each scenarios[activeScenario].gluttonyCode.split('\n') as line, idx}
              <div class="diff-line diff-add">
                <span class="line-num">{idx + 1}</span>
                <span class="diff-marker">+</span>
                <span class="line-content">{line}</span>
              </div>
            {/each}
          </div>
        </div>
      {/if}
    </div>

    <!-- Explanation Footer -->
    <div class="explanation-footer">
      <span class="diamond-bullet">◆</span>
      <p class="explanation-text">{scenarios[activeScenario].explanation}</p>
    </div>
  </div>
</section>

<style>
  .diff-section {
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

  .diff-card {
    background-color: var(--color-ink);
    overflow: hidden;
    width: 100%;
    box-sizing: border-box;
  }

  .diff-toolbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 18px;
    border-bottom: 1px solid var(--color-border);
    background-color: #050608;
    gap: 12px;
    flex-wrap: wrap;
  }

  .scenario-tabs {
    display: flex;
    align-items: center;
    gap: 8px;
    overflow-x: auto;
    scrollbar-width: none;
  }

  .scenario-tabs::-webkit-scrollbar {
    display: none;
  }

  .scenario-btn {
    background: transparent;
    border: 1px solid transparent;
    color: var(--color-ash);
    font-size: 11.5px;
    padding: 6px 12px;
    border-radius: 6px;
    cursor: pointer;
    white-space: nowrap;
    transition: all 0.22s cubic-bezier(0.16, 1, 0.3, 1);
  }

  .scenario-btn:hover {
    color: var(--color-pure-white);
    background-color: rgba(255, 255, 255, 0.06);
    transform: translateY(-1px);
  }

  .scenario-btn.active {
    background-color: #17181c;
    color: var(--color-pure-white);
    border-color: rgba(255, 99, 99, 0.35);
    box-shadow: 0 0 16px rgba(255, 99, 99, 0.12), var(--shadow-key-subtle);
  }

  .view-switcher {
    display: flex;
    align-items: center;
    gap: 4px;
    background-color: var(--color-obsidian);
    padding: 3px;
    border-radius: 6px;
    border: 1px solid var(--color-border);
  }

  .view-btn {
    background: transparent;
    border: none;
    color: var(--color-ash);
    font-size: 11px;
    padding: 3px 8px;
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.15s ease;
  }

  .view-btn.active {
    background-color: #27282b;
    color: var(--color-pure-white);
  }

  .impact-bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 18px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.05);
    background-color: #08090c;
    gap: 12px;
    flex-wrap: wrap;
  }

  .impact-left {
    display: flex;
    align-items: center;
    gap: 10px;
    font-size: 12px;
  }

  .scenario-name {
    font-weight: 500;
  }

  .impact-right {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .stat-tag {
    font-size: 11.5px;
    font-weight: 600;
    padding: 3px 10px;
    background: rgba(255, 99, 99, 0.1);
    border: 1px solid rgba(255, 99, 99, 0.3);
    border-radius: 6px;
    animation: metricGlow 3s ease-in-out infinite;
  }

  @keyframes metricGlow {
    0%, 100% {
      box-shadow: 0 0 8px rgba(255, 99, 99, 0.15);
      border-color: rgba(255, 99, 99, 0.25);
    }
    50% {
      box-shadow: 0 0 18px rgba(255, 99, 99, 0.35);
      border-color: rgba(255, 99, 99, 0.55);
    }
  }

  .copy-btn {
    padding: 4px 10px;
    font-size: 11px;
    min-height: 30px;
  }

  /* Code panels */
  .code-panels {
    display: grid;
    grid-template-columns: 1fr;
    background-color: #040506;
  }

  .code-panels.split-view {
    grid-template-columns: 1fr 1fr;
  }

  .code-panel {
    padding: 16px 20px;
    overflow-x: auto;
    min-width: 0;
  }

  .panel-raw {
    border-right: 1px solid var(--color-border);
    background-color: rgba(255, 95, 86, 0.015);
  }

  .panel-gluttony {
    background-color: rgba(89, 212, 153, 0.015);
  }

  .panel-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 10.5px;
    padding-bottom: 10px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.05);
    margin-bottom: 12px;
    letter-spacing: 0.5px;
    gap: 8px;
  }

  .verdict-bad {
    color: #ff5f56;
    font-size: 9.5px;
    letter-spacing: 0.5px;
  }

  .verdict-good {
    color: var(--color-success-green);
    font-size: 9.5px;
    letter-spacing: 0.5px;
  }

  .diff-code-table {
    display: flex;
    flex-direction: column;
    font-size: 11.5px;
    line-height: 1.6;
    overflow-x: auto;
  }

  .diff-line {
    display: flex;
    align-items: flex-start;
    padding: 1px 6px;
    border-radius: 3px;
    transition: background-color 0.15s ease;
  }

  .line-num {
    width: 26px;
    color: var(--color-smoke);
    text-align: right;
    padding-right: 10px;
    user-select: none;
    opacity: 0.55;
    flex-shrink: 0;
  }

  .diff-marker {
    width: 14px;
    text-align: center;
    flex-shrink: 0;
    user-select: none;
    font-weight: 600;
  }

  .diff-del {
    background: rgba(255, 95, 86, 0.04);
  }

  .diff-del .diff-marker {
    color: #ff5f56;
  }

  .diff-del:hover {
    background: rgba(255, 95, 86, 0.08);
  }

  .diff-add {
    background: rgba(89, 212, 153, 0.05);
  }

  .diff-add .diff-marker {
    color: var(--color-success-green);
  }

  .diff-add:hover {
    background: rgba(89, 212, 153, 0.1);
  }

  .line-content {
    white-space: pre;
    color: #d1d5db;
    overflow-x: visible;
  }

  .explanation-footer {
    display: flex;
    align-items: flex-start;
    gap: 10px;
    padding: 14px 20px;
    border-top: 1px solid var(--color-border);
    background-color: #050608;
  }

  .diamond-bullet {
    color: var(--color-coral-pulse);
    font-size: 10px;
    margin-top: 3px;
    flex-shrink: 0;
  }

  .explanation-text {
    font-size: 13px;
    color: var(--color-ash);
    line-height: 1.5;
  }

  @media (max-width: 860px) {
    .code-panels.split-view {
      grid-template-columns: 1fr;
    }

    .panel-raw {
      border-right: none;
      border-bottom: 1px solid var(--color-border);
    }

    .diff-toolbar {
      flex-direction: column;
      align-items: flex-start;
    }

    .view-switcher {
      align-self: flex-start;
    }
  }
</style>
