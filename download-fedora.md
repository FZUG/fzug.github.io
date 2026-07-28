---
layout: page
title: 下载 Fedora
permalink: /download-fedora/
---

Fedora 每六个月发布一个新版本，带来最新的自由开源软件。以下是最新 Fedora 版本的 Live ISO 镜像。

<div id="download-cards">
  <div class="dl-loading">正在获取最新版本信息…</div>
  <noscript>
    <div class="dl-error">请启用 JavaScript 以获取最新的下载链接，或访问 <a href="https://fedoraproject.org/workstation/download">fedoraproject.org</a> 直接下载。</div>
  </noscript>
</div>

<p id="dl-source" class="dl-source-note"></p>

<script>
(() => {
  const MIRRORS = [
    { name: 'TUNA (清华)', url: 'https://mirrors.tuna.tsinghua.edu.cn/fedora/releases/' },
    { name: 'USTC (中科大)', url: 'https://mirrors.ustc.edu.cn/fedora/releases/' },
    { name: 'SJTUG (上交)', url: 'https://mirror.sjtu.edu.cn/fedora/linux/releases/' }
  ];

  const OFFICIAL = 'https://download.fedoraproject.org/pub/fedora/linux/releases/';

  function formatSize(bytes) {
    const gb = bytes / (1024 * 1024 * 1024);
    if (gb >= 1) return gb.toFixed(1) + ' GB';
    const mb = bytes / (1024 * 1024);
    return mb.toFixed(0) + ' MB';
  }

  function extractPath(link) {
    const m = link.match(/\/linux\/releases\/(.+)/);
    return m ? m[1] : '';
  }

  function buildItem(variant, iso) {
    const path = extractPath(iso.link);
    const filename = path.split('/').pop();
    const pkg = encodeURIComponent(filename);
    return `
      <div class="dl-card">
        <div class="dl-card-header">
          <h2 class="dl-card-title">Fedora ${variant}</h2>
          <span class="dl-card-version">Fedora ${iso.version}</span>
        </div>
        <div class="dl-card-body">
          <p class="dl-card-file">${filename}</p>
          <p class="dl-card-size">${formatSize(iso.size)} · SHA256: <code class="dl-sha">${iso.sha256}</code></p>

          <div class="dl-links">
            <div class="dl-group">
              <h3 class="dl-group-title">官方下载</h3>
              <a class="dl-btn dl-btn-official" href="${iso.link}" onclick="gtag&&gtag('event','download',{event_category:'fedora',event_label:'${variant}_official'})">
                ${iso.link}
              </a>
            </div>
            <div class="dl-group">
              <h3 class="dl-group-title">国内镜像</h3>
              ${MIRRORS.map(m => `
                <a class="dl-btn dl-btn-mirror" href="${m.url + path}?format=iso" onclick="gtag&&gtag('event','download',{event_category:'fedora',event_label:'${variant}_${m.name}'})">
                  ${m.name}
                </a>
              `).join('')}
            </div>
            <div class="dl-group">
              <h3 class="dl-group-title">校验</h3>
              <a class="dl-btn dl-btn-small" href="${iso.link.replace('.iso', '-CHECKSUM')}">CHECKSUM</a>
              <a class="dl-btn dl-btn-small" href="/verify/">如何验证</a>
            </div>
          </div>
        </div>
      </div>`;
  }

  function render(variants, error) {
    const el = document.getElementById('download-cards');
    if (error) {
      el.innerHTML = `<div class="dl-error">获取版本信息失败。请访问 <a href="https://fedoraproject.org/workstation/download">fedoraproject.org</a> 直接下载。<br><small>${error}</small></div>`;
      return;
    }
    if (!variants.length) {
      el.innerHTML = '<div class="dl-error">未找到 ISO 镜像信息。</div>';
      return;
    }
    el.innerHTML = variants.map(v => buildItem(v.variant, v.iso)).join('');
  }

  let dataSource = '';

  fetch('https://fedora.gitlab.io/websites-apps/fedora-websites/fedora-websites-3.0/releases.json',
        { signal: AbortSignal.timeout(8000) })
    .then(r => { if (!r.ok) throw new Error('HTTP ' + r.status); dataSource = 'Fedora 官方'; return r.json(); })
    .catch(() => {
      dataSource = '网站缓存';
      return fetch('{{ '/assets/fedora-release.json' | relative_url }}').then(r => r.json());
    })
    .then(data => {
      const latest = data.reduce((max, i) => Math.max(max, parseInt(i.version) || 0), 0).toString();

      const workstation = data.find(i =>
        i.version === latest && i.arch === 'x86_64' &&
        i.variant === 'Workstation' && i.subvariant === 'Workstation' &&
        i.link.endsWith('.iso')
      );
      const kde = data.find(i =>
        i.version === latest && i.arch === 'x86_64' &&
        i.variant === 'KDE' && i.subvariant === 'KDE' &&
        i.link.endsWith('.iso')
      );

      const variants = [];
      if (workstation) variants.push({ variant: 'Workstation', iso: workstation });
      if (kde) variants.push({ variant: 'KDE Plasma Desktop', iso: kde });

      render(variants);
      document.getElementById('dl-source').textContent = `版本数据来源：${dataSource}`;
    })
    .catch(e => render([], 'GitLab 与本地数据均无法获取：' + e.message));
})();
</script>
