
<template>
  <div class="site-shell">
    <header class="site-header">
      <NuxtLink to="/" class="brand">
        <span class="brand-name">CHEN JINNING</span>
        <span class="brand-caption">PERSONAL PORTFOLIO</span>
      </NuxtLink>

      <nav class="navigation" aria-label="主要導覽">
        <NuxtLink to="/" exact-active-class="is-active">
          首頁
        </NuxtLink>

        <NuxtLink to="/about" exact-active-class="is-active">
          關於我
        </NuxtLink>

        <NuxtLink to="/projects" exact-active-class="is-active">
          作品
        </NuxtLink>
      </nav>
    </header>

    <div class="page-container">
      <slot />
    </div>

    <footer class="site-footer">
      <p>© 2026 CHEN JINNING · PERSONAL PORTFOLIO</p>
    </footer>
  </div>
</template>

<style scoped>
.site-shell {
  min-height: 100vh;
  background: #faf8f3;
  color: #51483f;
  font-family: "Helvetica Neue", Arial, sans-serif;
}

.site-header {
  max-width: 1120px;
  margin: 0 auto;
  padding: 30px 40px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #e9e3d9;
}

.brand {
  display: flex;
  flex-direction: column;
  gap: 5px;
  text-decoration: none;
  color: #51483f;
}

.brand-name {
  font-family: Georgia, serif;
  font-size: 19px;
  letter-spacing: 2px;
}

.brand-caption {
  font-size: 9px;
  letter-spacing: 2px;
  color: #a39787;
}

.navigation {
  display: flex;
  gap: 36px;
}

.navigation a {
  position: relative;
  padding: 8px 0;
  color: #82776b;
  text-decoration: none;
  font-size: 14px;
  transition: color 0.3s ease;
}

.navigation a:hover,
.navigation a.is-active {
  color: #9a7855;
}

.navigation a::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 1px;
  background: #a98c6d;
  transition: width 0.3s ease;
}

.navigation a:hover::after,
.navigation a.is-active::after {
  width: 100%;
}

.page-container {
  max-width: 1120px;
  margin: 0 auto;
  padding: 70px 40px;
  animation: pageFade 0.6s ease both;
}

.site-footer {
  padding: 24px;
  border-top: 1px solid #e9e3d9;
  text-align: center;
  color: #a39787;
  font-size: 10px;
  letter-spacing: 1.5px;
}

@keyframes pageFade {
  from {
    opacity: 0;
    transform: translateY(8px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 600px) {
  .site-header {
    padding: 24px 20px;
    flex-direction: column;
    gap: 22px;
  }

  .navigation {
    gap: 26px;
  }

  .page-container {
    padding: 45px 22px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .page-container {
    animation: none;
  }

  .navigation a,
  .navigation a::after {
    transition: none;
  }
}
</style>
