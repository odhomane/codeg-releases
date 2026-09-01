# 🚀 Codeg Server Universal Releases

Automated, continuous multi-architecture server builds for [Codeg](https://github.com/xintaofei/codeg) (`xintaofei/codeg`).

---

## 🎯 Why This Exists

Upstream Codeg publishes server binaries compiled against modern GLIBC 2.34+ and OpenSSL 3.x. This repository provides **universal, backwards-compatible server builds** compiled with a **GLIBC 2.28 baseline** inside enterprise Linux environments.

* **100% Drop-in Compatible**:
  * Ubuntu 20.04 LTS (GLIBC 2.31, OpenSSL 1.1)
  * Ubuntu 22.04 LTS (GLIBC 2.35, OpenSSL 3.0)
  * Ubuntu 24.04 LTS (GLIBC 2.39, OpenSSL 3.0)
  * Oracle Linux / RHEL / Rocky / Alma 8.x (GLIBC 2.28)
  * Oracle Linux / RHEL 9.x / 10.x
  * Raspberry Pi (ARM64)
* **Everything Included**: Every archive includes `codeg-server`, the `codeg-mcp` stdio companion binary, and the complete Next.js static web bundle in `web/`.
* **Zero Authentication Downloads**: Publicly accessible direct HTTPS release assets.

---

## 📦 Quick Install / One-Liner

### Linux ARM64 (OCI Ampere / Raspberry Pi / Apple Silicon VMs)
```bash
curl -sL https://github.com/odhomane/codeg-releases/releases/latest/download/codeg-server-linux-arm64.tar.gz | tar -xz -C /tmp/
sudo cp /tmp/codeg-server /usr/local/bin/codeg-server
sudo cp /tmp/codeg-mcp /usr/local/bin/codeg-mcp
sudo mkdir -p /usr/local/share/codeg
sudo cp -r /tmp/web /usr/local/share/codeg/
sudo chmod +x /usr/local/bin/codeg-server /usr/local/bin/codeg-mcp
```

### Linux x86_64 (AMD64)
```bash
curl -sL https://github.com/odhomane/codeg-releases/releases/latest/download/codeg-server-linux-x64.tar.gz | tar -xz -C /tmp/
sudo cp /tmp/codeg-server /usr/local/bin/codeg-server
sudo cp /tmp/codeg-mcp /usr/local/bin/codeg-mcp
sudo mkdir -p /usr/local/share/codeg
sudo cp -r /tmp/web /usr/local/share/codeg/
sudo chmod +x /usr/local/bin/codeg-server /usr/local/bin/codeg-mcp
```

---

## ⚙️ Native Systemd Service Unit

Create `/etc/systemd/system/codeg.service`:

```ini
[Unit]
Description=Codeg Multi-Agent Collaborative Workspace (Native)
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=root
WorkingDirectory=/root
Environment="PATH=/root/.codeg/npm-global/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
Environment="CODEG_PORT=3088"
Environment="CODEG_HOST=0.0.0.0"
Environment="CODEG_TOKEN=your_master_token_here"
Environment="CODEG_DATA_DIR=/var/lib/codeg-data"
Environment="CODEG_STATIC_DIR=/usr/local/share/codeg/web"
Environment="SHELL=/bin/bash"
ExecStart=/usr/local/bin/codeg-server
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now codeg.service
```

---

## 🔄 Automated CI/CD

The workflow in `.github/workflows/build-release.yml` checks upstream releases every 6 hours and automatically compiles, tests, packages, and attaches releases here.
