<script lang="ts">
  import { onMount } from 'svelte';

  type NetworkStatus = 'checking' | 'excellent' | 'good' | 'fair' | 'poor' | 'offline';

  let status = $state<NetworkStatus>('checking');
  let ping = $state<number | null>(null);
  let jitter = $state<number | null>(null);
  let downlink = $state<string | null>(null);
  let effectiveType = $state<string | null>(null);
  let isTesting = $state(false);

  const CEKNET_URL = 'https://ceknet.vercel.app/';
  const CF_PING_URL = 'https://speed.cloudflare.com/__down?bytes=0';

  async function checkInternetQuality() {
    if (isTesting) return;
    isTesting = true;
    status = 'checking';

    if (typeof navigator !== 'undefined') {
      const nav = navigator as unknown as {
        connection?: { effectiveType?: string; downlink?: number };
      };
      if (nav.connection) {
        effectiveType = nav.connection.effectiveType ? nav.connection.effectiveType.toUpperCase() : null;
        downlink = typeof nav.connection.downlink === 'number' ? `${nav.connection.downlink} Mbps` : null;
      }
    }

    const latencies: number[] = [];
    const samplesCount = 3;

    try {
      for (let i = 0; i < samplesCount; i++) {
        const start = performance.now();
        let res: Response | null = null;
        try {
          res = await fetch(`${CEKNET_URL}?t=${Date.now()}_${i}`, {
            method: 'HEAD',
            cache: 'no-store',
            signal: AbortSignal.timeout(4000),
          });
        } catch {
          try {
            res = await fetch(`${CF_PING_URL}&t=${Date.now()}_${i}`, {
              cache: 'no-store',
              signal: AbortSignal.timeout(4000),
            });
          } catch {
            // Sample failure
          }
        }

        if (res && (res.ok || res.type === 'opaque')) {
          const rtt = Math.max(1, performance.now() - start);
          latencies.push(rtt);
        }

        if (i < samplesCount - 1) {
          await new Promise((r) => setTimeout(r, 60));
        }
      }

      if (latencies.length === 0) {
        status = 'offline';
        ping = null;
        jitter = null;
      } else {
        const avg = latencies.reduce((a, b) => a + b, 0) / latencies.length;
        ping = Math.round(avg);

        if (latencies.length > 1) {
          const sqDiffs = latencies.map((val) => Math.pow(val - avg, 2));
          const sd = Math.sqrt(sqDiffs.reduce((a, b) => a + b, 0) / latencies.length);
          jitter = Math.round(sd * 10) / 10;
        } else {
          jitter = 0;
        }

        if (ping <= 60) {
          status = 'excellent';
        } else if (ping <= 120) {
          status = 'good';
        } else if (ping <= 200) {
          status = 'fair';
        } else {
          status = 'poor';
        }
      }
    } catch {
      status = 'offline';
      ping = null;
      jitter = null;
    } finally {
      isTesting = false;
    }
  }

  onMount(() => {
    checkInternetQuality();
  });
</script>

<aside class="ceknet-widget key-card" aria-label="Widget Kualitas Internet ceknet">
  <div class="widget-header">
    <div class="brand-group">
      <span class="status-pulse {status}"></span>
      <span class="widget-title font-mono">CEKNET API</span>
    </div>
    <span class="quality-badge font-mono {status}">
      {#if status === 'checking'}
        Mengecek...
      {:else if status === 'excellent'}
        Sangat Baik
      {:else if status === 'good'}
        Baik
      {:else if status === 'fair'}
        Cukup
      {:else if status === 'poor'}
        Lambat
      {:else}
        Terputus
      {/if}
    </span>
  </div>

  <div class="metrics-row font-mono">
    <div class="metric-col">
      <span class="metric-label">LATENSI</span>
      <span class="metric-val">{ping !== null ? `${ping}ms` : '—'}</span>
    </div>
    <div class="metric-divider"></div>
    <div class="metric-col">
      <span class="metric-label">JITTER</span>
      <span class="metric-val">{jitter !== null ? `±${jitter}ms` : '—'}</span>
    </div>
  </div>

  {#if effectiveType || downlink}
    <div class="network-type-strip font-mono">
      <span class="network-type-label">JARINGAN:</span>
      <span class="network-type-val">{effectiveType || 'WAN'}{downlink ? ` · ${downlink}` : ''}</span>
    </div>
  {/if}

  <div class="actions-row">
    <button
      class="action-btn recheck-btn font-mono"
      onclick={checkInternetQuality}
      disabled={isTesting}
      aria-label="Cek ulang kualitas koneksi via ceknet"
    >
      <svg
        class="icon-refresh"
        class:spinning={isTesting}
        width="11"
        height="11"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2.6"
      >
        <path d="M21.5 2v6h-6M2.5 22v-6h6M2 11.5a10 10 0 0 1 18.8-4.3M22 12.5a10 10 0 0 1-18.8 4.2" />
      </svg>
      <span>{isTesting ? 'Cek...' : 'Cek Ulang'}</span>
    </button>

    <a
      href="https://ceknet.vercel.app/"
      target="_blank"
      rel="noopener noreferrer"
      class="action-btn portal-btn font-mono"
      title="Buka ceknet.vercel.app untuk diagnostik kecepatan penuh"
    >
      <span>ceknet ↗</span>
    </a>
  </div>
</aside>

<style>
  .ceknet-widget {
    background-color: var(--color-ink);
    border: 1px solid var(--color-border);
    border-radius: 6px; /* Kotak dengan sudut tak tajam */
    padding: 10px 14px;
    display: flex;
    flex-direction: column;
    gap: 8px;
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.4);
    width: 216px;
    max-width: calc(100vw - 28px);
    box-sizing: border-box;
    transition: border-color 150ms ease, transform 150ms ease;
  }

  .ceknet-widget:hover {
    border-color: var(--color-coral-pulse);
    transform: translateY(-1px);
  }

  .ceknet-widget:active {
    transform: translateY(1px);
  }

  .widget-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 8px;
  }

  .brand-group {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .widget-title {
    font-size: 10.5px;
    font-weight: 700;
    letter-spacing: 0.6px;
    color: var(--color-pure-white);
  }

  .status-pulse {
    width: 6px;
    height: 6px;
    border-radius: 1px;
    flex-shrink: 0;
  }

  .status-pulse.checking {
    background-color: var(--color-ash);
    box-shadow: 0 0 4px var(--color-ash);
  }

  .status-pulse.excellent {
    background-color: #00ff9d;
    box-shadow: 0 0 6px #00ff9d;
  }

  .status-pulse.good {
    background-color: #38bdf8;
    box-shadow: 0 0 6px #38bdf8;
  }

  .status-pulse.fair {
    background-color: #ff9f30;
    box-shadow: 0 0 6px #ff9f30;
  }

  .status-pulse.poor {
    background-color: #ff9f30;
    box-shadow: 0 0 6px #ff9f30;
  }

  .status-pulse.offline {
    background-color: #f87171;
    box-shadow: 0 0 6px #f87171;
  }

  @keyframes pulseAnim {
    0%, 100% { opacity: 0.5; transform: scale(0.9); }
    50% { opacity: 1; transform: scale(1.15); }
  }

  .quality-badge {
    font-size: 9.5px;
    font-weight: 600;
    letter-spacing: 0.3px;
    padding: 1px 6px;
    border-radius: 2px;
    border: 1px solid transparent;
  }

  .quality-badge.checking {
    color: var(--color-ash);
    background: rgba(28, 43, 58, 0.4);
    border-color: var(--color-border);
  }

  .quality-badge.excellent {
    color: #00ff9d;
    background: rgba(0, 255, 157, 0.1);
    border-color: rgba(0, 255, 157, 0.3);
  }

  .quality-badge.good {
    color: #38bdf8;
    background: rgba(56, 189, 248, 0.1);
    border-color: rgba(56, 189, 248, 0.3);
  }

  .quality-badge.fair {
    color: #ff9f30;
    background: rgba(255, 159, 48, 0.1);
    border-color: rgba(255, 159, 48, 0.3);
  }

  .quality-badge.poor {
    color: #ff9f30;
    background: rgba(255, 159, 48, 0.15);
    border-color: rgba(255, 159, 48, 0.35);
  }

  .quality-badge.offline {
    color: #f87171;
    background: rgba(248, 113, 113, 0.1);
    border-color: rgba(248, 113, 113, 0.25);
  }

  .metrics-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: var(--color-obsidian);
    border: 1px solid var(--color-border);
    border-radius: 2px;
    padding: 6px 10px;
  }

  .metric-col {
    display: flex;
    flex-direction: column;
    gap: 1px;
  }

  .metric-label {
    font-size: 8.5px;
    color: var(--color-smoke);
    letter-spacing: 0.5px;
  }

  .metric-val {
    font-size: 13px;
    font-weight: 600;
    color: var(--color-pure-white);
  }

  .metric-divider {
    width: 1px;
    height: 22px;
    background: var(--color-border);
  }

  .network-type-strip {
    display: flex;
    align-items: center;
    gap: 4px;
    font-size: 9.5px;
    color: var(--color-smoke);
  }

  .network-type-label {
    letter-spacing: 0.5px;
  }

  .network-type-val {
    color: var(--color-ash);
    font-weight: 500;
  }

  .actions-row {
    display: flex;
    align-items: center;
    gap: 6px;
    margin-top: 2px;
  }

  .action-btn {
    flex: 1;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 4px;
    padding: 5px 8px;
    font-size: 10px;
    border-radius: 2px;
    text-decoration: none;
    cursor: pointer;
    min-height: 28px;
    transition: all 150ms ease;
  }

  .action-btn:active {
    transform: translateY(1px);
  }

  .recheck-btn {
    background: var(--color-obsidian);
    color: var(--color-ash);
    border: 1px solid var(--color-border);
  }

  .recheck-btn:hover:not(:disabled) {
    color: var(--color-pure-white);
    border-color: var(--color-coral-pulse);
    background: rgba(28, 43, 58, 0.5);
  }

  .recheck-btn:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }

  .portal-btn {
    background: rgba(255, 159, 48, 0.12);
    color: var(--color-coral-pulse);
    border: 1px solid rgba(255, 159, 48, 0.4);
    font-weight: 600;
  }

  .portal-btn:hover {
    background: var(--color-coral-pulse);
    border-color: var(--color-coral-pulse);
    color: #0b1623;
  }

  .icon-refresh.spinning {
    animation: spin 0.8s linear infinite;
  }

  @keyframes spin {
    from { transform: rotate(0deg); }
    to { transform: rotate(360deg); }
  }
</style>
