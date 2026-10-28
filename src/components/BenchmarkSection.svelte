<script lang="ts">
  import { onMount } from 'svelte';

  let sectionEl: HTMLElement;
  let hasAnimated = $state(false);
  let countDiff = $state(0);
  let countPrompt = $state(0);

  onMount(() => {
    if (!sectionEl) return;
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting && !hasAnimated) {
          hasAnimated = true;
          animateCounters();
          observer.disconnect();
        }
      },
      { threshold: 0.2 }
    );
    observer.observe(sectionEl);

    return () => observer.disconnect();
  });

  function animateCounters() {
    const startTime = performance.now();
    const duration = 1100;

    const step = (now: number) => {
      const elapsed = now - startTime;
      const progress = Math.min(1, elapsed / duration);
      // easeOutCubic
      const ease = 1 - Math.pow(1 - progress, 3);

      countDiff = Math.round(78 * ease);
      countPrompt = Math.round(49 * ease);

      if (progress < 1) {
        requestAnimationFrame(step);
      } else {
        countDiff = 78;
        countPrompt = 49;
      }
    };
    requestAnimationFrame(step);
  }

  const benchmarks = [
    {
      scenario: 'Isolated Bug Fix (Pagination Offset)',
      rawAi: '28-line wrapper class with unneeded abstractions',
      gluttony: 'Surgical 4-line diff directly at root cause',
      diffDelta: '-85% code bloat',
      status: 'PASS',
    },
    {
      scenario: 'New Feature Build (Rate Limiter)',
      rawAi: 'Installed 2 npm packages (express-rate-limit, redis)',
      gluttony: 'Stdlib first: native Map & setTimeout debouncer',
      diffDelta: 'Zero dependencies added',
      status: 'PASS',
    },
    {
      scenario: 'API Contract Migration (Profile DTO)',
      rawAi: 'Broke downstream consumers by renaming field without alias',
      gluttony: 'Preserved backward-compatibility via getter aliasing',
      diffDelta: '100% contract integrity',
      status: 'PASS',
    },
    {
      scenario: 'Security Hardening (Path Traversal)',
      rawAi: 'Simple regex replace that bypassed double encoded payloads',
      gluttony: 'Strict path.resolve & root directory boundary validation',
      diffDelta: 'Zero security regressions',
      status: 'PASS',
    },
    {
      scenario: 'Accessible Modal Component',
      rawAi: 'Monotonic cyan gradient, 2.8:1 contrast (WCAG fail)',
      gluttony: 'Matte dark elevation, 7.1:1 contrast, 48px touch targets',
      diffDelta: 'WCAG AA verified',
      status: 'PASS',
    },
  ];
</script>

<section id="benchmark" class="benchmark-section container" bind:this={sectionEl}>
  <!-- Section Header -->
  <div class="section-header">
    <span class="badge-tag">EMPIRICAL PROOF</span>
    <h2 class="section-title">Raw AI Baseline vs Gluttony Mode</h2>
    <p class="section-desc">
      Measured head-to-head across 5 rigorous technical test scenarios.
    </p>
  </div>

  <!-- Big Stat Metric Cards with Smooth Counters -->
  <div class="metrics-grid">
    <div class="metric-card key-card">
      <span class="metric-value font-mono text-coral">-{countDiff}%</span>
      <span class="metric-label">Diff Size Reduction</span>
      <span class="metric-sub font-mono">Surgical root-cause fixes</span>
    </div>

    <div class="metric-card key-card">
      <span class="metric-value font-mono text-coral">-{countPrompt}%</span>
      <span class="metric-label">Prompt Weight</span>
      <span class="metric-sub font-mono">High-density directives</span>
    </div>

    <div class="metric-card key-card">
      <span class="metric-value font-mono text-pure-white">0 Deps</span>
      <span class="metric-label">Third-Party Bloat</span>
      <span class="metric-sub font-mono">Native stdlib first</span>
    </div>

    <div class="metric-card key-card">
      <span class="metric-value font-mono text-pure-white">0 Regressions</span>
      <span class="metric-label">Breaking Changes</span>
      <span class="metric-sub font-mono">Contract preservation</span>
    </div>
  </div>

  <!-- Visual Benchmark Dashboard Infographic Display -->
  <div class="infographic-wrapper key-card">
    <div class="infographic-header">
      <div class="infographic-title-wrap">
        <span class="font-mono text-coral">BENCHMARK RUNNER</span>
        <span class="badge-tag">v2.2.0 EVALUATION</span>
      </div>
      <span class="font-mono text-smoke text-sm">npm run benchmark</span>
    </div>

    <div class="image-container">
      <img
        src="/benchmark.png"
        alt="Gluttony Comparative Benchmark: Raw AI Baseline vs Gluttony Mode"
        class="benchmark-img"
        loading="lazy"
      />
    </div>
  </div>

  <!-- Scenario Comparison Table -->
  <div class="table-container key-card">
    <div class="table-header">
      <div class="table-header-left">
        <span class="font-mono text-sm text-ash">SCENARIO BREAKDOWN</span>
      </div>
      <span class="scroll-hint font-mono text-smoke">← SCROLL TO VIEW ALL →</span>
    </div>

    <div class="table-scroll">
      <table class="benchmark-table">
        <thead>
          <tr class="font-mono">
            <th>TEST SCENARIO</th>
            <th>RAW AI BASELINE</th>
            <th>GLUTTONY PROTOCOL</th>
            <th>MEASURED ADVANTAGE</th>
          </tr>
        </thead>
        <tbody>
          {#each benchmarks as row}
            <tr>
              <td class="scenario-cell font-mono">{row.scenario}</td>
              <td class="raw-cell text-smoke">{row.rawAi}</td>
              <td class="gluttony-cell text-pure-white">
                <span class="diamond-dot">◆</span> {row.gluttony}
              </td>
              <td class="delta-cell font-mono text-coral">{row.diffDelta}</td>
            </tr>
          {/each}
        </tbody>
      </table>
    </div>
  </div>
</section>

<style>
  .benchmark-section {
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

  .metrics-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 14px;
    margin-bottom: 28px;
    width: 100%;
    box-sizing: border-box;
  }

  .metric-card {
    padding: 20px 16px;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    gap: 4px;
    background-color: var(--color-ink);
    box-sizing: border-box;
    min-width: 0;
  }

  .metric-value {
    font-size: clamp(22px, 4.5vw, 30px);
    font-weight: 600;
    color: var(--color-pure-white);
    letter-spacing: -0.5px;
  }

  .metric-label {
    font-size: 13px;
    color: var(--color-ash);
    font-weight: 500;
  }

  .metric-sub {
    font-size: 10.5px;
    color: var(--color-coral-pulse);
    margin-top: 2px;
  }

  /* Infographic Image Card */
  .infographic-wrapper {
    margin-bottom: 28px;
    background-color: var(--color-ink);
    overflow: hidden;
    width: 100%;
    box-sizing: border-box;
  }

  .infographic-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 18px;
    border-bottom: 1px solid var(--color-border);
    background-color: #050608;
    gap: 8px;
    flex-wrap: wrap;
  }

  .infographic-title-wrap {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 11.5px;
  }

  .text-sm {
    font-size: 10.5px;
  }

  .image-container {
    padding: 12px;
    background-color: var(--color-void-black);
    display: flex;
    justify-content: center;
    overflow: hidden;
  }

  .benchmark-img {
    width: 100%;
    max-width: 1080px;
    height: auto;
    border-radius: 8px;
    border: 1px solid rgba(255, 255, 255, 0.08);
  }

  /* Table */
  .table-container {
    background-color: var(--color-ink);
    overflow: hidden;
    width: 100%;
    box-sizing: border-box;
  }

  .table-header {
    padding: 12px 18px;
    border-bottom: 1px solid var(--color-border);
    background-color: #050608;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
  }

  .scroll-hint {
    font-size: 10px;
    letter-spacing: 0.5px;
    display: none;
  }

  .table-scroll {
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
    width: 100%;
    box-sizing: border-box;
  }

  .benchmark-table {
    width: 100%;
    min-width: 620px;
    border-collapse: collapse;
    font-size: 13px;
    text-align: left;
  }

  .benchmark-table th {
    padding: 12px 16px;
    font-size: 10.5px;
    color: var(--color-smoke);
    border-bottom: 1px solid var(--color-border);
    letter-spacing: 0.6px;
    white-space: nowrap;
  }

  .benchmark-table td {
    padding: 14px 16px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.04);
  }

  .scenario-cell {
    font-size: 12px;
    color: var(--color-ash);
    white-space: nowrap;
  }

  .raw-cell {
    font-size: 12.5px;
    line-height: 1.45;
  }

  .gluttony-cell {
    font-size: 12.5px;
    line-height: 1.45;
  }

  .diamond-dot {
    color: var(--color-coral-pulse);
    font-size: 8px;
    margin-right: 4px;
  }

  .delta-cell {
    font-size: 11.5px;
    font-weight: 500;
    white-space: nowrap;
  }

  @media (max-width: 900px) {
    .metrics-grid {
      grid-template-columns: repeat(2, 1fr);
    }
  }

  @media (max-width: 640px) {
    .scroll-hint {
      display: inline-block;
    }

    .metric-card {
      padding: 14px 10px;
    }

    .metric-label {
      font-size: 12px;
    }
  }
</style>
