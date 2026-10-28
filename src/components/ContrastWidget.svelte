<script lang="ts">
  let fgColor = $state('#FFFFFF');
  let bgColor = $state('#040506');

  function channelLuminance(val: number) {
    const c = val / 255;
    return c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4);
  }

  function parseHex(hexStr: string): [number, number, number] {
    let clean = hexStr.replace('#', '').trim();
    if (clean.length === 3) {
      clean = clean.split('').map((c) => c + c).join('');
    }
    if (clean.length >= 6) {
      const r = parseInt(clean.slice(0, 2), 16) || 0;
      const g = parseInt(clean.slice(2, 4), 16) || 0;
      const b = parseInt(clean.slice(4, 6), 16) || 0;
      return [r, g, b];
    }
    return [255, 255, 255];
  }

  function getLuminance(rgb: [number, number, number]): number {
    return (
      0.2126 * channelLuminance(rgb[0]) +
      0.7152 * channelLuminance(rgb[1]) +
      0.0722 * channelLuminance(rgb[2])
    );
  }

  let ratio = $derived.by(() => {
    try {
      const l1 = getLuminance(parseHex(fgColor));
      const l2 = getLuminance(parseHex(bgColor));
      const lighter = Math.max(l1, l2);
      const darker = Math.min(l1, l2);
      return Number(((lighter + 0.05) / (darker + 0.05)).toFixed(2));
    } catch {
      return 1.0;
    }
  });

  let passNormal = $derived(ratio >= 4.5);
  let passLarge = $derived(ratio >= 3.0);
  let passAAA = $derived(ratio >= 7.0);

  let gaugeProgress = $derived(Math.min(1, Math.max(0, (ratio - 1) / 20)));
  let dashOffset = $derived(263.89 * (1 - gaugeProgress));
  let gaugeColor = $derived(ratio >= 7.0 ? '#4ade80' : ratio >= 4.5 ? '#ff6363' : '#ff5f56');

  const presets = [
    { name: 'White on Void', fg: '#FFFFFF', bg: '#040506' },
    { name: 'Ash on Void', fg: '#9C9C9D', bg: '#040506' },
    { name: 'Mist on Ink', fg: '#E6E6E6', bg: '#07080A' },
    { name: 'Coral on Void', fg: '#FF6363', bg: '#040506' },
    { name: 'AI Slop (Low)', fg: '#54728C', bg: '#1A222B' },
  ];

  function applyPreset(p: { fg: string; bg: string }) {
    fgColor = p.fg;
    bgColor = p.bg;
  }
</script>

<section id="contrast" class="contrast-section container">
  <!-- Section Header -->
  <div class="section-header">
    <span class="badge-tag">GLUTTONY-UI PROTOCOL</span>
    <h2 class="section-title">Zero-Guesswork Contrast Verifier</h2>
    <p class="section-desc">
      Gluttony mandates mathematical verification over visual intuition.
      Test your tokens against WCAG 2.1 AA thresholds before shipping.
    </p>
  </div>

  <div class="widget-card key-card">
    <div class="widget-header">
      <div class="widget-title-group">
        <span class="font-mono text-coral">WCAG 2.1 RATIO CALCULATOR</span>
        <span class="badge-tag">EMBEDDED UTILITY</span>
      </div>
      <span class="font-mono text-smoke text-sm">node script/contrast.js</span>
    </div>

    <div class="widget-body">
      <!-- Controls -->
      <div class="controls-column">
        <div class="input-group">
          <label class="input-label font-mono" for="fg-input">FOREGROUND (TEXT)</label>
          <div class="color-input-wrapper">
            <input
              id="fg-input"
              type="color"
              bind:value={fgColor}
              class="color-swatch-input"
            />
            <input
              type="text"
              bind:value={fgColor}
              class="recessed-well font-mono text-input"
              maxlength="7"
            />
          </div>
        </div>

        <div class="input-group">
          <label class="input-label font-mono" for="bg-input">BACKGROUND (SURFACE)</label>
          <div class="color-input-wrapper">
            <input
              id="bg-input"
              type="color"
              bind:value={bgColor}
              class="color-swatch-input"
            />
            <input
              type="text"
              bind:value={bgColor}
              class="recessed-well font-mono text-input"
              maxlength="7"
            />
          </div>
        </div>

        <!-- Presets -->
        <div class="presets-wrap">
          <span class="presets-label font-mono">TOKEN PRESETS:</span>
          <div class="presets-pills">
            {#each presets as p}
              <button
                onclick={() => applyPreset(p)}
                class="preset-btn font-mono"
              >
                {p.name}
              </button>
            {/each}
          </div>
        </div>
      </div>

      <!-- Preview & Gauge Column -->
      <div class="preview-column">
        <div class="preview-gauge-row">
          <div
            class="live-preview-box"
            style="background-color: {bgColor}; color: {fgColor};"
          >
            <span class="preview-tag font-mono">LIVE PREVIEW</span>
            <h4 class="preview-headline">Minimal Sufficient Engineering</h4>
            <p class="preview-body">
              Enforcing authentic typography and contrast for human clarity.
            </p>
          </div>

          <!-- Circular Arc Meter Gauge -->
          <div class="gauge-card">
            <div class="gauge-svg-wrap">
              <svg width="92" height="92" viewBox="0 0 100 100" class="gauge-svg" aria-label="Contrast gauge {ratio} to 1">
                <!-- Track -->
                <circle
                  cx="50"
                  cy="50"
                  r="42"
                  fill="none"
                  stroke="rgba(255, 255, 255, 0.08)"
                  stroke-width="7"
                />
                <!-- Active Sweep Arc -->
                <circle
                  cx="50"
                  cy="50"
                  r="42"
                  fill="none"
                  stroke={gaugeColor}
                  stroke-width="7"
                  stroke-linecap="round"
                  stroke-dasharray="263.89"
                  stroke-dashoffset={dashOffset}
                  transform="rotate(-90 50 50)"
                  style="transition: stroke-dashoffset 0.35s cubic-bezier(0.16, 1, 0.3, 1), stroke 0.25s ease;"
                />
              </svg>
              <div class="gauge-center-text">
                <span class="gauge-value font-mono" style="color: {gaugeColor}">{ratio}</span>
                <span class="gauge-unit font-mono">:1</span>
              </div>
            </div>
            <div class="gauge-footer-tag font-mono" style="color: {gaugeColor}">
              {ratio >= 7.0 ? 'AAA PASS' : ratio >= 4.5 ? 'AA PASS' : 'CONTRAST FAIL'}
            </div>
          </div>
        </div>

        <!-- Metric Badges -->
        <div class="results-grid">
          <div class="result-tile">
            <span class="res-label font-mono">NORMAL TEXT (&ge; 4.5:1)</span>
            <span class="status-badge font-mono" class:pass={passNormal} class:fail={!passNormal}>
              {passNormal ? 'PASS · AA' : 'FAIL'}
            </span>
          </div>

          <div class="result-tile">
            <span class="res-label font-mono">LARGE TEXT (&ge; 3.0:1)</span>
            <span class="status-badge font-mono" class:pass={passLarge} class:fail={!passLarge}>
              {passLarge ? 'PASS' : 'FAIL'}
            </span>
          </div>

          <div class="result-tile">
            <span class="res-label font-mono">ENHANCED (&ge; 7.0:1)</span>
            <span class="status-badge font-mono" class:pass={passAAA} class:fail={!passAAA}>
              {passAAA ? 'PASS · AAA' : 'FAIL'}
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<style>
  .contrast-section {
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

  .widget-card {
    background-color: var(--color-ink);
    overflow: hidden;
    width: 100%;
    box-sizing: border-box;
  }

  .widget-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 18px;
    border-bottom: 1px solid var(--color-border);
    background-color: #050608;
    gap: 8px;
    flex-wrap: wrap;
  }

  .widget-title-group {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 11.5px;
  }

  .text-sm {
    font-size: 10.5px;
  }

  .widget-body {
    padding: 24px;
    display: grid;
    grid-template-columns: 1fr 1.25fr;
    gap: 24px;
    width: 100%;
    box-sizing: border-box;
  }

  .controls-column {
    display: flex;
    flex-direction: column;
    gap: 16px;
    min-width: 0;
  }

  .input-group {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  .input-label {
    font-size: 10.5px;
    color: var(--color-smoke);
    letter-spacing: 0.8px;
  }

  .color-input-wrapper {
    display: flex;
    align-items: center;
    gap: 10px;
    width: 100%;
    box-sizing: border-box;
  }

  .color-swatch-input {
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    width: 36px;
    height: 36px;
    border: 1px solid var(--color-border);
    border-radius: var(--radius-inputs);
    background: none;
    cursor: pointer;
    padding: 0;
    flex-shrink: 0;
  }

  .color-swatch-input::-webkit-color-swatch-wrapper {
    padding: 0;
  }

  .color-swatch-input::-webkit-color-swatch {
    border: none;
    border-radius: 6px;
  }

  .text-input {
    flex-grow: 1;
    font-size: 13px;
    text-transform: uppercase;
    min-width: 0;
  }

  .presets-wrap {
    display: flex;
    flex-direction: column;
    gap: 8px;
    padding-top: 10px;
    border-top: 1px solid rgba(255, 255, 255, 0.05);
  }

  .presets-label {
    font-size: 10.5px;
    color: var(--color-smoke);
    letter-spacing: 0.8px;
  }

  .presets-pills {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
  }

  .preset-btn {
    background-color: var(--color-obsidian);
    color: var(--color-ash);
    border: 1px solid var(--color-border);
    border-radius: 6px;
    padding: 4px 8px;
    font-size: 10.5px;
    cursor: pointer;
    transition: all 0.15s ease;
  }

  .preset-btn:hover {
    color: var(--color-pure-white);
    border-color: rgba(255, 255, 255, 0.25);
  }

  /* Preview */
  .preview-column {
    display: flex;
    flex-direction: column;
    gap: 16px;
    min-width: 0;
  }

  .preview-gauge-row {
    display: flex;
    align-items: stretch;
    gap: 14px;
    width: 100%;
  }

  .live-preview-box {
    padding: 16px 18px;
    border-radius: var(--radius-cards);
    border: 1px solid var(--color-border);
    min-height: 120px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 6px;
    transition: background-color 0.2s ease, color 0.2s ease;
    box-sizing: border-box;
    flex-grow: 1;
    min-width: 0;
  }

  .preview-tag {
    font-size: 9.5px;
    letter-spacing: 0.8px;
    opacity: 0.7;
  }

  .preview-headline {
    font-size: clamp(15px, 3.2vw, 18px);
    font-weight: 500;
  }

  .preview-body {
    font-size: 12.5px;
    opacity: 0.9;
    line-height: 1.4;
  }

  /* Gauge Card */
  .gauge-card {
    background-color: var(--color-obsidian);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-cards);
    padding: 12px 16px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 8px;
    flex-shrink: 0;
    min-width: 130px;
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.04);
  }

  .gauge-svg-wrap {
    position: relative;
    width: 92px;
    height: 92px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .gauge-svg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    transform: rotate(0deg);
  }

  .gauge-center-text {
    display: flex;
    align-items: baseline;
    justify-content: center;
    z-index: 1;
  }

  .gauge-value {
    font-size: 20px;
    font-weight: 700;
    letter-spacing: -0.5px;
  }

  .gauge-unit {
    font-size: 12px;
    color: var(--color-smoke);
    font-weight: 500;
  }

  .gauge-footer-tag {
    font-size: 10px;
    font-weight: 600;
    letter-spacing: 0.8px;
    text-transform: uppercase;
  }

  .results-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    width: 100%;
    box-sizing: border-box;
  }

  .result-tile {
    background-color: var(--color-obsidian);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-inputs);
    padding: 10px 12px;
    display: flex;
    flex-direction: column;
    gap: 4px;
    min-width: 0;
  }

  .res-label {
    font-size: 9.5px;
    color: var(--color-smoke);
    letter-spacing: 0.6px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .status-badge {
    font-size: 11px;
    font-weight: 500;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .status-badge.pass {
    color: var(--color-success-green);
  }

  .status-badge.fail {
    color: #ff5f56;
  }

  @media (max-width: 860px) {
    .widget-body {
      grid-template-columns: 1fr;
      padding: 16px;
    }
  }

  @media (max-width: 540px) {
    .preview-gauge-row {
      flex-direction: column;
    }

    .gauge-card {
      width: 100%;
      box-sizing: border-box;
    }

    .results-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
