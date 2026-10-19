<script lang="ts">
  let copied = $state(false);

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
    <!-- Brand -->
    <a href="/" class="brand-link">
      <div class="diamond-icon">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
          <path d="M12 2L22 12L12 22L2 12L12 2Z" fill="#ff6363" />
          <path d="M12 6L18 12L12 18L6 12L12 6Z" fill="#040506" />
        </svg>
      </div>
      <span class="brand-name">Gluttony</span>
      <span class="badge-tag">v2.2.0</span>
    </a>

    <!-- Nav Links -->
    <div class="nav-links">
      <a href="#cockpit" class="nav-link">Cockpit</a>
      <a href="#skills" class="nav-link">Sub-Skills</a>
      <a href="#benchmark" class="nav-link">Benchmark</a>
      <a href="#contrast" class="nav-link">Contrast Tool</a>
      <a href="#platforms" class="nav-link">Platforms</a>
    </div>

    <!-- Actions -->
    <div class="nav-actions">
      <a
        href="https://github.com/Novazeb/gluttony"
        target="_blank"
        rel="noopener noreferrer"
        class="nav-icon-link"
        aria-label="GitHub Repository"
      >
        <svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor">
          <path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.53 1.032 1.53 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z" />
        </svg>
      </a>
      <button onclick={copyInstall} class="btn-mist">
        {#if copied}
          <span>Copied!</span>
        {:else}
          <span>Install Skill</span>
        {/if}
      </button>
    </div>
  </nav>
</header>

<style>
  .nav-wrapper {
    position: fixed;
    top: 20px;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    z-index: 100;
    padding: 0 16px;
    pointer-events: none;
  }

  .nav-pill {
    pointer-events: auto;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 32px;
    background-color: rgba(7, 8, 10, 0.75);
    backdrop-filter: blur(48px);
    -webkit-backdrop-filter: blur(48px);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-buttons);
    padding: 6px 14px;
    max-width: 960px;
    width: 100%;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.6), rgba(255, 255, 255, 0.05) 0px 1px 0px 0px inset;
  }

  .brand-link {
    display: flex;
    align-items: center;
    gap: 8px;
    text-decoration: none;
    color: var(--color-pure-white);
  }

  .diamond-icon {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .brand-name {
    font-size: 13px;
    font-weight: 500;
    letter-spacing: -0.2px;
  }

  .nav-links {
    display: flex;
    align-items: center;
    gap: 24px;
  }

  .nav-link {
    color: var(--color-ash);
    font-size: 13px;
    font-weight: 500;
    text-decoration: none;
    transition: color 0.15s ease;
  }

  .nav-link:hover {
    color: var(--color-pure-white);
  }

  .nav-actions {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .nav-icon-link {
    color: var(--color-ash);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: color 0.15s ease;
  }

  .nav-icon-link:hover {
    color: var(--color-pure-white);
  }

  @media (max-width: 768px) {
    .nav-links {
      display: none;
    }
  }
</style>
