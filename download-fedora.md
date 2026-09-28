---
layout: page
title: 下载 Fedora
permalink: /download-fedora/
---

Fedora 每六个月发布一个新版本，带来最新的自由开源软件。请先选择架构、版本号与变体，然后获取对应的 Live ISO 镜像下载链接。

<div id="download-controls">
  <div class="dl-control">
    <span class="dl-label">架构</span>
    <div id="dl-arch" class="dl-toggle-group"></div>
  </div>
  <div class="dl-control">
    <span class="dl-label">版本号</span>
    <div id="dl-version" class="dl-toggle-group"></div>
  </div>
  <div class="dl-control">
    <span class="dl-label">变体</span>
    <div id="dl-edition" class="dl-toggle-group"></div>
  </div>
  <label class="dl-advanced">
    <input type="checkbox" id="dl-advanced"> 显示高级选项（所有架构、版本号与变体组合）
  </label>
</div>

<div id="dl-warning" class="dl-arch-warning" hidden></div>

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

  const ARCHES = [
    { id: 'x86_64', label: 'x86_64' },
    { id: 'aarch64', label: 'aarch64 (ARM)' },
    { id: 'ppc64le', label: 'ppc64le (POWER)' },
    { id: 's390x', label: 's390x' }
  ];

  const DEFAULT_EDITIONS = ['Workstation', 'KDE', 'Server'];

  const EDITION_LABELS = {
    'Workstation': 'Workstation',
    'KDE': 'KDE Plasma',
    'Server': 'Server',
    'Silverblue': 'Silverblue',
    'Kinoite': 'Kinoite',
    'Onyx': 'Onyx',
    'Sericea': 'Sericea',
    'COSMIC-Atomic': 'COSMIC Atomic',
    'Cinnamon': 'Cinnamon',
    'Xfce': 'Xfce',
    'Mate': 'MATE',
    'Budgie': 'Budgie',
    'Sway': 'Sway',
    'i3': 'i3',
    'LXQt': 'LXQt',
    'LXDE': 'LXDE',
    'COSMIC': 'COSMIC',
    'KDE_Mobile': 'KDE Mobile',
    'SoaS': 'SoaS',
    'MiracleWM': 'MiracleWM',
    'Everything': 'Everything',
    'IoT': 'IoT',
    'IoT_Simplified_Provisioner': 'IoT Provisioner'
  };

  const EDITION_ORDER = [
    'Workstation', 'KDE', 'Server',
    'Silverblue', 'Kinoite', 'Onyx', 'Sericea', 'COSMIC-Atomic',
    'Cinnamon', 'Xfce', 'Mate', 'Budgie', 'Sway', 'i3', 'LXQt', 'LXDE',
    'COSMIC', 'KDE_Mobile', 'SoaS', 'MiracleWM',
    'Everything', 'IoT', 'IoT_Simplified_Provisioner'
  ];

  const state = { arch: 'x86_64', version: null, edition: null, advanced: false };
  let data = [];
  let dataSource = '';

  function loadState() {
    try {
      const saved = JSON.parse(sessionStorage.getItem('dl-state') || 'null');
      if (saved && saved.arch) {
        state.arch = saved.arch;
        state.version = saved.version || null;
        state.edition = saved.edition || null;
        state.advanced = !!saved.advanced;
        const cb = document.getElementById('dl-advanced');
        if (cb) cb.checked = state.advanced;
      }
    } catch (e) { /* ignore */ }
  }

  function saveState() {
    try {
      sessionStorage.setItem('dl-state', JSON.stringify({
        arch: state.arch,
        version: state.version,
        edition: state.edition,
        advanced: state.advanced
      }));
    } catch (e) { /* ignore */ }
  }

  // version 字段形如 "44" 或 "45 Beta"，用 parseInt 比较会把 "45 Beta" 误判成 45，
  // 因此统一先解析成 { version, num, stable } 再按 num 排序、按字符串精确匹配。
  function versionEntries() {
    const map = new Map();
    data.forEach(i => {
      const v = i.version == null ? '' : String(i.version).trim();
      if (!v || map.has(v)) return;
      map.set(v, { version: v, num: parseInt(v) || 0, stable: /^\d+$/.test(v) });
    });
    return [...map.values()].sort((a, b) =>
      b.num - a.num || (a.stable === b.stable ? 0 : a.stable ? -1 : 1));
  }

  function visibleVersions() {
    const all = versionEntries();
    if (state.advanced) return all;
    const stable = all.filter(v => v.stable);
    // 数据里万一只有预发布版本，也至少给出可选项
    return (stable.length ? stable : all).slice(0, 2);
  }

  function isBeta(v) {
    return !/^\d+$/.test(String(v == null ? '' : v).trim());
  }

  function formatSize(bytes) {
    const gb = bytes / (1024 * 1024 * 1024);
    if (gb >= 1) return gb.toFixed(1) + ' GB';
    const mb = bytes / (1024 * 1024);
    return mb.toFixed(0) + ' MB';
  }

  function extractPath(link) {
    const m = link.match(/\/releases\/(.+)/);
    return m ? m[1] : '';
  }

  function isAltHosted(link) {
    return /\/alt\//.test(link);
  }

  function checksumLink(iso) {
    const dir = iso.link.slice(0, iso.link.lastIndexOf('/') + 1);
    const filename = iso.link.split('/').pop();
    if (iso.variant === 'IoT') {
      const date = (filename.match(/\d{8}\.\d+/) || [])[0] || '';
      return dir + `Fedora-IoT-${iso.version}-${iso.arch}-${date}-CHECKSUM`;
    }
    // 正式版: Fedora-Workstation-44-1.7-x86_64-CHECKSUM
    // 预发布: Fedora-Workstation-iso-45_Beta-1.3-x86_64-CHECKSUM（多一段 iso-）
    const compose = (filename.match(/(\d+(?:_\w+)?-\d+\.\d+)/) || [])[1] || iso.version;
    const kind = isBeta(iso.version) ? 'iso-' : '';
    return dir + `Fedora-${iso.variant}-${kind}${compose}-${iso.arch}-CHECKSUM`;
  }

  function isoScore(filename) {
    if (/Live/.test(filename)) return 100;
    if (/dvd/.test(filename)) return 90;
    if (/netinst/.test(filename)) return 80;
    if (/ostree/.test(filename)) return 70;
    if (/provisioner/.test(filename)) return 60;
    if (/Atomic/.test(filename)) return 50;
    if (/Mobile/.test(filename)) return 40;
    if (/Classroom/.test(filename)) return 30;
    return 10;
  }

  function editionsFor(arch, advanced) {
    const byKey = {};
    data.forEach(i => {
      if (String(i.version).trim() !== state.version || i.arch !== arch || !i.link.endsWith('.iso')) return;
      const key = i.subvariant || i.variant;
      (byKey[key] = byKey[key] || []).push(i);
    });
    const available = Object.keys(byKey);
    let keys;
    if (advanced) {
      keys = EDITION_ORDER.filter(k => available.includes(k))
        .concat(available.filter(k => !EDITION_ORDER.includes(k)).sort());
    } else {
      keys = DEFAULT_EDITIONS.filter(k => available.includes(k));
    }
    return keys.map(key => {
      const iso = byKey[key].reduce((best, i) =>
        isoScore(i.link.split('/').pop()) > isoScore(best.link.split('/').pop()) ? i : best);
      return { key, iso };
    });
  }

  function buildItem(edition) {
    const iso = edition.iso;
    const path = extractPath(iso.link);
    const filename = path.split('/').pop();
    const label = EDITION_LABELS[edition.key] || edition.key;
    const alt = isAltHosted(iso.link);
    const checksum = checksumLink(iso);
    return `
      <div class="dl-card">
        <div class="dl-card-header">
          <h2 class="dl-card-title">Fedora ${label}</h2>
          <span class="dl-card-version">Fedora ${iso.version} · ${state.arch}</span>
        </div>
        <div class="dl-card-body">
          <p class="dl-card-file">${filename}</p>
          <p class="dl-card-size">${formatSize(iso.size)} · SHA256: <code class="dl-sha">${iso.sha256}</code></p>

          <div class="dl-links">
            <div class="dl-group">
              <h3 class="dl-group-title">官方下载</h3>
              <a class="dl-btn dl-btn-official" href="${iso.link}" onclick="gtag&&gtag('event','download',{event_category:'fedora',event_label:'${label}_${state.arch}_official'})">
                ${iso.link}
              </a>
            </div>
            ${alt ? `
            <div class="dl-group">
              <h3 class="dl-group-title">国内镜像</h3>
              <p class="dl-note">该镜像位于官方 alt 仓库，国内镜像站未同步，暂无国内镜像链接。</p>
            </div>` : `
            <div class="dl-group">
              <h3 class="dl-group-title">国内镜像</h3>
              ${MIRRORS.map(m => `
                <a class="dl-btn dl-btn-mirror" href="${m.url + path}?format=iso" onclick="gtag&&gtag('event','download',{event_category:'fedora',event_label:'${label}_${state.arch}_${m.name}'})">
                  ${m.name}
                </a>
              `).join('')}
            </div>`}
            <div class="dl-group">
              <h3 class="dl-group-title">校验</h3>
              <a class="dl-btn dl-btn-small" href="${checksum}">CHECKSUM</a>
              <a class="dl-btn dl-btn-small" href="/verify/">如何验证</a>
            </div>
          </div>
        </div>
      </div>`;
  }

  function render() {
    const el = document.getElementById('download-cards');
    const warning = document.getElementById('dl-warning');

    const warns = [];
    if (state.arch === 'aarch64') {
      warns.push('此处提供的 ARM 架构 ISO 镜像仅适用于特定规范下的 UEFI 系统');
      warns.push('树莓派适用 raw.xz 镜像，请参阅<a href="https://docs.fedoraproject.org/zh_CN/quick-docs/raspberry-pi/">树莓派相关文档</a>');
      warns.push('Apple Silicon (M 系列) Mac 请使用 <a href="https://fedora-asahi-remix.org/">Fedora Asahi Remix</a>，' +
        '由 <a href="https://asahilinux.org/">Asahi Linux 项目</a> 与 <a href="https://fedoraproject.org/wiki/SIGs/Asahi">Fedora Asahi SIG</a> 合作维护');
    }
    if (isBeta(state.version)) {
      warns.push('当前选择的是 Beta 预发布版本，仅用于测试，可能存在已知问题，请勿用于生产环境');
    }
    if (state.advanced) {
      warns.push('高级选项会显示所有可能的架构、版本号与变体组合，其中一些组合不一定适用于您的设备或者用途，请在下载前详细了解');
    }
    warning.hidden = warns.length === 0;
    warning.innerHTML = warns.length ? '<ul><li>' + warns.join('</li><li>') + '</li></ul>' : '';

    if (!state.version || !state.edition) {
      el.innerHTML = '<div class="dl-hint">请先选择架构、版本号与变体，以获取下载链接。</div>';
      return;
    }
    const editions = editionsFor(state.arch, state.advanced);
    const edition = editions.find(e => e.key === state.edition);
    if (!edition) {
      state.edition = null;
      render();
      return;
    }
    el.innerHTML = buildItem(edition);
  }

  function renderControls() {
    const archEl = document.getElementById('dl-arch');
    const versionEl = document.getElementById('dl-version');
    const editionEl = document.getElementById('dl-edition');

    const visibleArches = state.advanced ? ARCHES : ARCHES.slice(0, 2);
    archEl.innerHTML = visibleArches.map(a =>
      `<button type="button" class="dl-toggle${state.arch === a.id ? ' is-active' : ''}" data-arch="${a.id}">${a.label}</button>`
    ).join('');

    const versions = visibleVersions();
    versionEl.innerHTML = versions.map(v =>
      `<button type="button" class="dl-toggle${state.version === v.version ? ' is-active' : ''}${v.stable ? '' : ' is-prerelease'}" data-version="${v.version}">${v.version}</button>`
    ).join('');

    const editions = editionsFor(state.arch, state.advanced);
    editionEl.innerHTML = editions.map(e =>
      `<button type="button" class="dl-toggle${state.edition === e.key ? ' is-active' : ''}" data-edition="${e.key}">${EDITION_LABELS[e.key] || e.key}</button>`
    ).join('');
  }

  function sync() {
    state.advanced = document.getElementById('dl-advanced').checked;
    const validArches = state.advanced ? ARCHES : ARCHES.slice(0, 2);
    if (!validArches.some(a => a.id === state.arch)) {
      state.arch = 'x86_64';
      state.edition = null;
    }
    const versions = visibleVersions();
    if (!versions.some(v => v.version === state.version)) {
      // 所选版本号不在当前可见列表中（例如关闭高级模式后的 Beta），回退到最新的一个。
      // 这里不清空 edition，交由下方校验：若该版本仍提供所选变体则保留用户选择。
      state.version = versions.length ? versions[0].version : null;
    }
    if (state.edition && !editionsFor(state.arch, state.advanced).some(e => e.key === state.edition)) {
      state.edition = null;
    }
    saveState();
    renderControls();
    render();
  }

  document.getElementById('dl-arch').addEventListener('click', e => {
    const btn = e.target.closest('[data-arch]');
    if (!btn) return;
    state.arch = btn.dataset.arch;
    state.edition = null;
    sync();
  });

  document.getElementById('dl-version').addEventListener('click', e => {
    const btn = e.target.closest('[data-version]');
    if (!btn) return;
    state.version = btn.dataset.version;
    // 不主动清空 edition：切换版本号后若所选变体仍存在则保留，
    // 不存在时由 sync() 统一校验并回退。
    sync();
  });

  document.getElementById('dl-edition').addEventListener('click', e => {
    const btn = e.target.closest('[data-edition]');
    if (!btn) return;
    state.edition = btn.dataset.edition;
    sync();
  });

  document.getElementById('dl-advanced').addEventListener('change', e => {
    state.advanced = e.target.checked;
    sync();
  });

  loadState();

  // AbortSignal.timeout 需要较新的浏览器，缺失时退化为无超时，避免整个脚本中断
  const timeoutSignal = (typeof AbortSignal === 'function' && typeof AbortSignal.timeout === 'function')
    ? AbortSignal.timeout(8000) : undefined;

  fetch('https://fedora.gitlab.io/websites-apps/fedora-websites/fedora-websites-3.0/releases.json',
        { signal: timeoutSignal })
    .then(r => { if (!r.ok) throw new Error('HTTP ' + r.status); dataSource = 'Fedora 官方'; return r.json(); })
    .catch(() => {
      dataSource = '网站缓存';
      return fetch('{{ '/assets/fedora-release.json' | relative_url }}').then(r => r.json());
    })
    .then(json => {
      data = Array.isArray(json) ? json : [];
      sync();
      document.getElementById('dl-source').textContent = `版本数据来源：${dataSource}`;
    })
    .catch(e => {
      const el = document.getElementById('download-cards');
      el.innerHTML = `<div class="dl-error">获取版本信息失败。请访问 <a href="https://fedoraproject.org/workstation/download">fedoraproject.org</a> 直接下载。<br><small>${e.message}</small></div>`;
    });
})();
</script>
