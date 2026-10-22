<script lang="ts">
  const currentYear = new Date().getFullYear();
</script>

<footer class="site-footer">
  <div class="container footer-container">
    <!-- Top Branding & Navigation -->
    <div class="footer-top">
      <div class="footer-brand">
        <div class="brand-line">
          <div class="diamond-icon">
            <svg width="12" height="12" viewBox="0 0 24 24" fill="none">
              <path d="M12 2L22 12L12 22L2 12L12 2Z" fill="#ff6363" />
            </svg>
          </div>
          <span class="brand-text">Gluttony</span>
          <span class="badge-tag">v2.2.0</span>
        </div>
        <p class="brand-desc">
          Minimal sufficient engineering delivers the smallest robust solution that is correct, secure, and verifiable.
        </p>
      </div>

      <div class="footer-links-group">
        <a href="#cockpit" class="footer-link">Cockpit</a>
        <a href="#diff" class="footer-link">Diff</a>
        <a href="#rules" class="footer-link">Rules</a>
        <a href="#skills" class="footer-link">Sub-Skills</a>
        <a href="#setup" class="footer-link">Setup</a>
        <a href="#benchmark" class="footer-link">Benchmark</a>
        <a href="#contrast" class="footer-link">WCAG Tool</a>
        <a href="#faq" class="footer-link">FAQ</a>
        <a
          href="https://github.com/Novazeb/gluttony"
          target="_blank"
          rel="noopener noreferrer"
          class="footer-link text-coral"
        >
          GitHub →
        </a>
      </div>
    </div>

    <div class="footer-divider"></div>

    <!-- Raycast Footer Meta Strip -->
    <div class="footer-meta-strip font-mono">
      <span>v2.2.0</span>
      <span class="pipe">|</span>
      <span>MIT License</span>
      <span class="pipe">|</span>
      <span>Created by <a href="https://github.com/Novazeb" target="_blank" rel="noopener noreferrer" class="meta-link">Novazeb</a></span>
      <span class="pipe">|</span>
      <span>YAGNI First</span>
      <span class="pipe">|</span>
      <span>Zero Slop</span>
      <span class="pipe">|</span>
      <span>© {currentYear}</span>
    </div>
  </div>
</footer>

<style>
  .site-footer {
    padding-top: 48px;
    padding-bottom: 48px;
    border-top: 1px solid var(--color-border);
    background-color: #030405;
    width: 100%;
    box-sizing: border-box;
  }

  .footer-container {
    display: flex;
    flex-direction: column;
    gap: 28px;
    box-sizing: border-box;
  }

  .footer-top {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 24px;
  }

  .footer-brand {
    display: flex;
    flex-direction: column;
    gap: 8px;
    max-width: 440px;
  }

  .brand-line {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .diamond-icon {
    display: flex;
    align-items: center;
  }

  .brand-text {
    font-size: 14.5px;
    font-weight: 500;
    color: var(--color-pure-white);
  }

  .brand-desc {
    font-size: 13px;
    color: var(--color-smoke);
    line-height: 1.5;
  }

  .footer-links-group {
    display: flex;
    flex-wrap: wrap;
    gap: 16px;
  }

  .footer-link {
    font-size: 12.5px;
    color: var(--color-ash);
    text-decoration: none;
    transition: color 0.15s ease;
  }

  .footer-link:hover {
    color: var(--color-pure-white);
  }

  .footer-divider {
    height: 1px;
    background-color: rgba(255, 255, 255, 0.05);
  }

  /* Raycast Signature Pipe-Separated Meta Strip */
  .footer-meta-strip {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
    gap: 10px;
    font-size: 11px;
    color: var(--color-smoke);
    text-align: center;
  }

  .pipe {
    color: var(--color-slate);
  }

  .meta-link {
    color: var(--color-ash);
    text-decoration: none;
  }

  .meta-link:hover {
    color: var(--color-coral-pulse);
  }

  @media (max-width: 768px) {
    .footer-top {
      flex-direction: column;
      gap: 20px;
    }

    .footer-links-group {
      gap: 12px;
    }
  }
</style>
