<script lang="ts">
  import { onMount } from 'svelte';

  let copied = $state(false);
  let stars = $state<string>('★ Star');
  let scrollProgress = $state(0);
  let activeSection = $state('');

  const navItems = [
    { href: '#cockpit', label: 'Cockpit' },
    { href: '#diff', label: 'Diff' },
    { href: '#rules', label: 'Rules' },
    { href: '#skills', label: 'Skills' },
    { href: '#setup', label: 'Setup' },
    { href: '#benchmark', label: 'Benchmark' },
    { href: '#faq', label: 'FAQ' },
  ];

  onMount(async () => {
    // Scroll progress & section spy
    const handleScroll = () => {
      const docHeight = document.documentElement.scrollHeight - window.innerHeight;
      scrollProgress = docHeight > 0 ? Math.min(100, Math.max(0, (window.scrollY / docHeight) * 100)) : 0;

      const sections = ['faq', 'benchmark', 'setup', 'skills', 'rules', 'diff', 'cockpit'];
      const scrollPos = window.scrollY + 160;
      for (const id of sections) {
        const el = document.getElementById(id);
        if (el && el.offsetTop <= scrollPos) {
          activeSection = id;
          return;
        }
      }
      activeSection = '';
    };

    window.addEventListener('scroll', handleScroll, { passive: true });
    handleScroll();

    // GitHub stars fetch
    try {
      const res = await fetch('https://api.github.com/repos/Novazeb/gluttony');
      if (res.ok) {
        const data = await res.json();
        if (typeof data.stargazers_count === 'number') {
          stars = `★ ${data.stargazers_count}`;
        }
      }
    } catch {
      // Fallback
    }

    return () => {
      window.removeEventListener('scroll', handleScroll);
    };
  });

  function copyInstall() {
    navigator.clipboard.writeText('/plugin install gluttony@gluttony');
    copied = true;
    setTimeout(() => {
      copied = false;
    }, 2000);
  }
</script>

<header class="nav-wrapper">
  <nav class="nav-pill">
    <!-- Real-time Scroll Progress Tracer -->
    <div
      class="nav-progress-bar"
      style="width: {scrollProgress}%;"
      aria-hidden="true"
    ></div>

    <!-- Brand -->
    <a href="/" class="brand-link">
      <div class="diamond-icon">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
          <path d="M12 2L22 12L12 22L2 12L12 2Z" fill="#ff6363" />
          <path d="M12 6L18 12L12 18L6 12L12 6Z" fill="#040506" />
        </svg>
      </div>
      <span class="brand-name">Gluttony</span>
      <span class="badge-tag hide-mobile">v2.2.0</span>
    </a>

    <!-- Nav Links (Desktop) -->
    <div class="nav-links">
      {#each navItems as item}
        <a
          href={item.href}
          class="nav-link"
          class:active={activeSection === item.href.slice(1)}
        >
          {item.label}
        </a>
      {/each}
    </div>

    <!-- Actions -->
    <div class="nav-actions">
      <a
        href="https://github.com/Novazeb/gluttony"
        target="_blank"
        rel="noopener noreferrer"
        class="github-pill-link"
        aria-label="GitHub Repository"
      >
        <svg width="15" height="15" viewBox="0 0 24 24" fill="currentColor">
          <path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.53 1.032 1.53 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z" />
        </svg>
        <span class="star-badge font-mono">{stars}</span>
      </a>

      <button onclick={copyInstall} class="btn-mist nav-cta">
        {#if copied}
          <span>Copied!</span>
        {:else}
          <span>Install</span>
        {/if}
      </button>
    </div>
  </nav>
</header>

<style>
  .nav-wrapper {
    position: fixed;
    top: 16px;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    z-index: 100;
    padding: 0 16px;
    pointer-events: none;
    box-sizing: border-box;
    width: 100%;
  }

  .nav-pill {
    position: relative;
    overflow: hidden;
    pointer-events: auto;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
    background-color: rgba(7, 8, 10, 0.88);
    backdrop-filter: blur(48px);
    -webkit-backdrop-filter: blur(48px);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-buttons);
    padding: 6px 14px;
    max-width: 980px;
    width: 100%;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.7), rgba(255, 255, 255, 0.05) 0px 1px 0px 0px inset;
  }

  .nav-progress-bar {
    position: absolute;
    bottom: 0;
    left: 0;
    height: 1.5px;
    background: linear-gradient(90deg, #ff6363 0%, #63a1ff 100%);
    box-shadow: 0 0 10px rgba(255, 99, 99, 0.75);
    transition: width 0.08s linear;
    pointer-events: none;
    z-index: 2;
  }

  .brand-link {
    display: flex;
    align-items: center;
    gap: 8px;
    text-decoration: none;
    color: var(--color-pure-white);
    flex-shrink: 0;
  }

  .diamond-icon {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .brand-name {
    font-size: 13.5px;
    font-weight: 500;
    letter-spacing: -0.2px;
  }

  .nav-links {
    display: flex;
    align-items: center;
    gap: 16px;
  }

  .nav-link {
    color: var(--color-ash);
    font-size: 12.5px;
    font-weight: 500;
    text-decoration: none;
    transition: color 0.15s ease;
    white-space: nowrap;
  }

  .nav-link:hover {
    color: var(--color-pure-white);
  }

  .nav-link.active {
    color: var(--color-pure-white);
    text-shadow: 0 0 12px rgba(255, 99, 99, 0.45);
  }

  .nav-actions {
    display: flex;
    align-items: center;
    gap: 10px;
    flex-shrink: 0;
  }

  .github-pill-link {
    display: flex;
    align-items: center;
    gap: 6px;
    background: rgba(255, 255, 255, 0.05);
    border: 1px solid var(--color-border);
    border-radius: 6px;
    padding: 5px 9px;
    color: var(--color-ash);
    text-decoration: none;
    transition: all 0.15s ease;
  }

  .github-pill-link:hover {
    color: var(--color-pure-white);
    background: rgba(255, 255, 255, 0.08);
    border-color: rgba(255, 255, 255, 0.2);
  }

  .star-badge {
    font-size: 11px;
    font-weight: 600;
    color: var(--color-pure-white);
  }

  .nav-cta {
    padding: 6px 12px;
    font-size: 12px;
    min-height: 32px;
  }

  @media (max-width: 860px) {
    .nav-links {
      display: none;
    }
  }

  @media (max-width: 768px) {
    .nav-wrapper {
      top: 10px;
      padding: 0 10px;
    }

    .nav-pill {
      padding: 6px 10px;
      gap: 10px;
    }

    .hide-mobile {
      display: none;
    }
  }
</style>
