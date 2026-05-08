<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Jamari's Home Lab — Network Diagram</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Exo+2:wght@300;400;600;700&display=swap');

  :root {
    --bg: #0a0e1a;
    --panel: #0f1628;
    --border: #1e3a5f;
    --accent: #00aaff;
    --accent2: #00ffcc;
    --accent3: #ff6b35;
    --text: #c8dff5;
    --muted: #4a6a8a;
    --node-bg: #111e35;
    --server: #0d2b4e;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    font-family: 'Exo 2', sans-serif;
    color: var(--text);
    min-height: 100vh;
    padding: 30px 20px;
  }

  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,170,255,0.04) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,170,255,0.04) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .wrapper { position: relative; z-index: 1; max-width: 900px; margin: 0 auto; }

  header { text-align: center; margin-bottom: 32px; }
  header h1 {
    font-family: 'Share Tech Mono', monospace;
    font-size: clamp(16px, 4vw, 26px);
    color: var(--accent);
    letter-spacing: 3px;
    text-transform: uppercase;
    text-shadow: 0 0 20px rgba(0,170,255,0.5);
  }
  header p {
    font-size: 12px;
    color: var(--muted);
    margin-top: 6px;
    font-family: 'Share Tech Mono', monospace;
    letter-spacing: 1px;
  }
  .tag {
    display: inline-block;
    background: rgba(0,170,255,0.08);
    border: 1px solid rgba(0,170,255,0.25);
    color: var(--accent);
    font-size: 10px;
    font-family: 'Share Tech Mono', monospace;
    padding: 3px 10px;
    border-radius: 2px;
    margin: 8px 3px 0;
    letter-spacing: 1px;
  }

  .diagram {
    background: var(--panel);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 28px 20px;
    box-shadow: 0 0 40px rgba(0,170,255,0.05);
  }

  .layer-label {
    font-family: 'Share Tech Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    letter-spacing: 2px;
    text-transform: uppercase;
    text-align: center;
    margin-bottom: 12px;
    border-bottom: 1px solid var(--border);
    padding-bottom: 6px;
  }

  .layer {
    display: flex;
    justify-content: center;
    align-items: flex-start;
    gap: 16px;
    flex-wrap: wrap;
  }

  .connector {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 40px;
    position: relative;
    margin: 4px 0;
  }
  .connector::before {
    content: '';
    position: absolute;
    left: 50%;
    top: 0; bottom: 0;
    width: 2px;
    background: linear-gradient(to bottom, var(--accent), var(--accent2));
    opacity: 0.4;
  }
  .connector-label {
    background: var(--bg);
    border: 1px solid var(--border);
    font-family: 'Share Tech Mono', monospace;
    font-size: 10px;
    color: var(--accent2);
    padding: 3px 10px;
    border-radius: 10px;
    position: relative;
    z-index: 1;
    letter-spacing: 1px;
  }

  /* Wide connector for splitting to 2 VMs */
  .split-connector {
    position: relative;
    height: 44px;
    margin: 4px auto;
    width: 100%;
    max-width: 520px;
  }
  .split-connector svg {
    width: 100%;
    height: 44px;
    overflow: visible;
  }

  .node {
    background: var(--node-bg);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 14px 16px;
    min-width: 180px;
    max-width: 240px;
    flex: 1;
    position: relative;
    transition: border-color 0.2s, box-shadow 0.2s;
  }
  .node:hover {
    border-color: var(--accent);
    box-shadow: 0 0 16px rgba(0,170,255,0.12);
  }
  .node.server { background: var(--server); border-color: #1a4a7a; }
  .node.highlight { border-color: var(--accent2); box-shadow: 0 0 14px rgba(0,255,204,0.08); }

  .status-dot {
    width: 7px; height: 7px;
    border-radius: 50%;
    background: var(--accent2);
    position: absolute;
    top: 10px; right: 10px;
    box-shadow: 0 0 6px var(--accent2);
    animation: pulse 2s infinite;
  }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.3} }

  .node-icon { font-size: 22px; margin-bottom: 8px; display: block; }
  .node-title { font-size: 13px; font-weight: 700; color: #e8f4ff; margin-bottom: 3px; }
  .node-sub { font-family: 'Share Tech Mono', monospace; font-size: 10px; color: var(--muted); margin-bottom: 8px; }
  .node-ip { font-family: 'Share Tech Mono', monospace; font-size: 10px; color: var(--accent2); margin-top: 6px; opacity: 0.85; }

  .node-tags { display: flex; flex-wrap: wrap; gap: 4px; margin-top: 6px; }
  .node-tag { font-family: 'Share Tech Mono', monospace; font-size: 9px; padding: 2px 6px; border-radius: 2px; letter-spacing: 0.5px; }
  .tag-blue   { background: rgba(0,170,255,0.12); color: #5bc8ff; border: 1px solid rgba(0,170,255,0.2); }
  .tag-green  { background: rgba(0,255,204,0.08); color: #00ffcc; border: 1px solid rgba(0,255,204,0.18); }
  .tag-orange { background: rgba(255,107,53,0.1);  color: #ff8c5a; border: 1px solid rgba(255,107,53,0.22); }
  .tag-purple { background: rgba(160,100,255,0.1); color: #c084fc; border: 1px solid rgba(160,100,255,0.22); }

  .section-gap { height: 20px; }

  .info-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(190px, 1fr));
    gap: 12px;
    margin-top: 22px;
  }
  .info-box {
    background: rgba(0,170,255,0.03);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 12px 14px;
  }
  .info-box h4 {
    font-family: 'Share Tech Mono', monospace;
    font-size: 10px;
    color: var(--accent);
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 8px;
  }
  .info-box p { font-size: 12px; color: var(--muted); line-height: 1.8; }
  .info-box span { color: var(--text); }

  .legend {
    display: flex;
    flex-wrap: wrap;
    gap: 16px;
    margin-top: 22px;
    padding-top: 16px;
    border-top: 1px solid var(--border);
    justify-content: center;
  }
  .legend-item { display: flex; align-items: center; gap: 7px; font-family: 'Share Tech Mono', monospace; font-size: 11px; color: var(--muted); }
  .legend-line { width: 24px; height: 2px; }
  .ll-solid  { background: var(--accent); }
  .ll-green  { background: var(--accent2); }
  .ll-dashed { background: repeating-linear-gradient(90deg,var(--accent3) 0,var(--accent3) 4px,transparent 4px,transparent 8px); }

  footer { text-align:center; font-family:'Share Tech Mono',monospace; font-size:11px; color:#1e3a5f; margin-top:14px; }
</style>
</head>
<body>
<div class="wrapper">

  <header>
    <h1>🖥 Home Lab — Network Diagram</h1>
    <p>// Virtualized IT Lab — Workstation Build</p>
    <div>
      <span class="tag">Fedora 43 Workstation</span>
      <span class="tag">Hyprland WM</span>
      <span class="tag">virt-manager / KVM</span>
      <span class="tag">homelab.local</span>
      <span class="tag">192.168.1.0/24</span>
    </div>
  </header>

  <div class="diagram">

    <!-- LAYER 1: Physical Workstation -->
    <div class="layer-label">Physical Workstation — Host Machine</div>
    <div class="layer">
      <div class="node server highlight" style="max-width:380px; min-width:300px;">
        <div class="status-dot"></div>
        <span class="node-icon">🖥️</span>
        <div class="node-title">Fedora 43 Workstation</div>
        <div class="node-sub">KVM Hypervisor · virt-manager · Hyprland WM</div>
        <div class="node-ip">192.168.1.10 — Host</div>
        <div class="node-tags">
          <span class="node-tag tag-green">Fedora 43</span>
          <span class="node-tag tag-green">KVM / QEMU</span>
          <span class="node-tag tag-blue">virt-manager</span>
          <span class="node-tag tag-purple">Hyprland</span>
          <span class="node-tag tag-orange">SSH</span>
          <span class="node-tag tag-orange">RDP Client</span>
        </div>
      </div>
    </div>

    <!-- Split connector down to 2 VMs -->
    <div class="split-connector">
      <svg viewBox="0 0 520 44" preserveAspectRatio="none">
        <!-- vertical drop from host -->
        <line x1="260" y1="0" x2="260" y2="20" stroke="#00aaff" stroke-width="1.5" stroke-opacity="0.5"/>
        <!-- horizontal bar -->
        <line x1="130" y1="20" x2="390" y2="20" stroke="#00aaff" stroke-width="1.5" stroke-opacity="0.5"/>
        <!-- drop to left VM -->
        <line x1="130" y1="20" x2="130" y2="44" stroke="#00aaff" stroke-width="1.5" stroke-opacity="0.5"/>
        <!-- drop to right VM -->
        <line x1="390" y1="20" x2="390" y2="44" stroke="#00aaff" stroke-width="1.5" stroke-opacity="0.5"/>
        <!-- label -->
        <rect x="185" y="10" width="150" height="20" rx="10" fill="#0a0e1a" stroke="#1e3a5f" stroke-width="1"/>
        <text x="260" y="24" text-anchor="middle" font-family="Share Tech Mono, monospace" font-size="10" fill="#00ffcc" letter-spacing="1">Virtual Bridge Network</text>
      </svg>
    </div>

    <!-- LAYER 2: Virtual Machines -->
    <div class="layer-label">Virtual Machines — KVM / virt-manager</div>
    <div class="layer">

      <div class="node server">
        <div class="status-dot"></div>
        <span class="node-icon">🗄️</span>
        <div class="node-title">Windows Server 2022</div>
        <div class="node-sub">Domain Controller</div>
        <div class="node-ip">192.168.1.20 — Static</div>
        <div class="node-tags">
          <span class="node-tag tag-blue">AD DS</span>
          <span class="node-tag tag-blue">DNS</span>
          <span class="node-tag tag-blue">DHCP</span>
          <span class="node-tag tag-green">GPO</span>
          <span class="node-tag tag-green">OUs</span>
          <span class="node-tag tag-green">RBAC</span>
          <span class="node-tag tag-orange">File Shares</span>
          <span class="node-tag tag-orange">NTFS Perms</span>
        </div>
      </div>

      <div class="node">
        <div class="status-dot"></div>
        <span class="node-icon">💻</span>
        <div class="node-title">Windows 11 Pro</div>
        <div class="node-sub">Domain Client VM</div>
        <div class="node-ip">192.168.1.30 — DHCP</div>
        <div class="node-tags">
          <span class="node-tag tag-blue">Domain Joined</span>
          <span class="node-tag tag-green">homelab.local</span>
          <span class="node-tag tag-orange">RDP</span>
          <span class="node-tag tag-purple">End User Sim</span>
          <span class="node-tag tag-blue">GPO Applied</span>
        </div>
      </div>

    </div>

    <div class="connector"><span class="connector-label">Metrics & Monitoring</span></div>

    <!-- LAYER 3: Monitoring -->
    <div class="layer-label">Monitoring Stack</div>
    <div class="layer">

      <div class="node">
        <span class="node-icon">📊</span>
        <div class="node-title">Grafana</div>
        <div class="node-sub">Dashboard & Visualization</div>
        <div class="node-tags">
          <span class="node-tag tag-orange">Dashboards</span>
          <span class="node-tag tag-blue">VM Health</span>
          <span class="node-tag tag-blue">System Stats</span>
        </div>
      </div>

      <div class="node">
        <span class="node-icon">🔍</span>
        <div class="node-title">Prometheus</div>
        <div class="node-sub">Metrics Collection</div>
        <div class="node-tags">
          <span class="node-tag tag-orange">Scraping</span>
          <span class="node-tag tag-blue">Alerting</span>
          <span class="node-tag tag-green">Time Series</span>
        </div>
      </div>

    </div>

    <!-- Info boxes -->
    <div class="info-grid">
      <div class="info-box">
        <h4>Domain Info</h4>
        <p>
          Domain: <span>homelab.local</span><br>
          Subnet: <span>192.168.1.0/24</span><br>
          DC IP: <span>192.168.1.20</span>
        </p>
      </div>
      <div class="info-box">
        <h4>DC Services</h4>
        <p>
          AD DS · DNS · DHCP<br>
          GPO · OUs · RBAC<br>
          NTFS · File Shares
        </p>
      </div>
      <div class="info-box">
        <h4>Host Tools</h4>
        <p>
          Fedora 43 · Hyprland<br>
          KVM / QEMU · virt-manager<br>
          SSH · RDP · Bash / CLI
        </p>
      </div>
      <div class="info-box">
        <h4>Remote Access</h4>
        <p>
          RDP → Windows VMs<br>
          SSH → Host & VMs<br>
          virt-manager Console
        </p>
      </div>
    </div>

    <!-- Legend -->
    <div class="legend">
      <div class="legend-item"><div class="legend-line ll-solid"></div> VM Bridge Network</div>
      <div class="legend-item"><div class="legend-line ll-green"></div> Domain Traffic (AD/DNS)</div>
      <div class="legend-item"><div class="legend-line ll-dashed"></div> Monitoring Traffic</div>
    </div>

  </div>

  <footer>// Jamari James — IT Support Technician — Workstation Homelab — homelab.local</footer>

</div>
</body>
</html>
