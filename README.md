<div align="center">

# 🎯 Ch4120N-NetPinner

**Make your machine's IPv4 address permanent on the current network — without breaking other Wi-Fi/Ethernet networks.**

[![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20Termux-blue?style=for-the-badge&logo=linux&logoColor=white)](#-supported-platforms)
[![Shell](https://img.shields.io/badge/shell-bash-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white)](#)
[![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)](#-license)
[![Author](https://img.shields.io/badge/author-Ch4120N-purple?style=for-the-badge&logo=github)](https://github.com/Ch4120N)

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=20&pause=1000&color=8A2BE2&center=true&vCenter=true&width=600&lines=Static+IP+made+simple.;Multi-backend+support.;Termux-friendly.;Zero+config+files+by+hand." alt="Typing SVG" />

</div>

---

## 📖 Table of Contents

- [✨ Features](#-features)
- [🎬 Demo](#-demo)
- [🖥️ Supported Platforms](#️-supported-platforms)
- [📦 Installation](#-installation)
- [🚀 Usage](#-usage)
- [🧠 How It Works](#-how-it-works)
- [⚙️ Flags & Environment](#️-flags--environment)
- [📋 Examples](#-examples)
- [📱 Termux Notes](#-termux-notes)
- [🛠️ Troubleshooting](#️-troubleshooting)
- [🤝 Contributing](#-contributing)
- [📜 License](#-license)
- [👤 Author](#-author)

---

## ✨ Features

| | Feature |
|---|---|
| 🎯 | **One-command static IP** — interactive prompts with smart defaults |
| 🔌 | **Multi-backend** — NetworkManager, netplan, systemd-networkd, ifupdown |
| 🧠 | **Smart detection** — auto-detects the active network backend |
| 🔒 | **Non-destructive** — backs up configs before every change |
| 🌐 | **Roaming-safe** — NetworkManager profiles per SSID keep other networks on DHCP |
| 🎨 | **Beautiful UI** — spinner, countdown, typewriter banner, colour-coded output |
| 📱 | **Termux-aware** — read-only status for Android |
| 🔇 | **Quiet mode** — perfect for scripts and CI |
| ♻️ | **One-command revert** — go back to DHCP instantly |
| 🧪 | **Conflict check** — warns if the target IP already answers on the LAN |

---

## 🎬 Demo

```text
$ sudo Ch4120N-NetPinner set

Ch4120N-NetPinner
Static IP helper for Linux and Termux

[*] Active profile: HomeWiFi on wlan0 (802-11-wireless)

Static IP     [192.168.1.50]:
Prefix (CIDR) [24]:
Gateway       [192.168.1.1]:
DNS           [192.168.1.1,1.1.1.1]:

────────────────────────────
Planned changes
────────────────────────────
  Profile : HomeWiFi
  IP      : 192.168.1.50/24
  Gateway : 192.168.1.1
  DNS     : 192.168.1.1,1.1.1.1

Proceed? [y/N] y
[+] Applying static configuration to 'HomeWiFi'
[*] Verifying network 3
  IPv4    : 192.168.1.50/24
  Gateway : 192.168.1.1
[+] Gateway reachable.
[+] Internet reachable.

[+] Done.
```

---

## 🖥️ Supported Platforms

| Distro / OS | Backend | Status |
|---|---|---|
| Ubuntu / Debian | netplan · NetworkManager · ifupdown | ✅ Full support |
| Fedora / RHEL / CentOS | NetworkManager | ✅ Full support |
| Arch / Manjaro | systemd-networkd · NetworkManager | ✅ Full support |
| openSUSE | wicked · NetworkManager | ⚠️ Via NetworkManager |
| Alpine | ifupdown-ng | ⚠️ Partial |
| Raspberry Pi OS | dhcpcd · NetworkManager | ⚠️ See notes |
| Termux (Android) | — | ℹ️ Status only |

---

## 📦 Installation

### Quick install (system-wide)

```bash
git clone https://github.com/Ch4120N/Ch4120N-NetPinner.git
cd Ch4120N-NetPinner
chmod +x Ch4120N-NetPinner
sudo mv Ch4120N-NetPinner /usr/local/bin/
```

### Manual install

```bash
curl -fsSL https://raw.githubusercontent.com/Ch4120N/Ch4120N-NetPinner/main/Ch4120N-NetPinner \
  -o /tmp/Ch4120N-NetPinner
chmod +x /tmp/Ch4120N-NetPinner
sudo mv /tmp/Ch4120N-NetPinner /usr/local/bin/
```

### Termux

```bash
pkg install termux-api
curl -fsSL https://raw.githubusercontent.com/Ch4120N/Ch4120N-NetPinner/main/Ch4120N-NetPinner \
  -o $PREFIX/bin/Ch4120N-NetPinner
chmod +x $PREFIX/bin/Ch4120N-NetPinner
```

---

## 🚀 Usage

```text
sudo Ch4120N-NetPinner <command> [flags]
```

| Command | Description |
|---|---|
| `set` | Interactive: ask for IP / prefix / gateway / DNS |
| `set --auto` | Non-interactive: accept the suggested defaults |
| `revert` | Return the current profile to DHCP |
| `status` | Show current IP / method / gateway |
| `list` | List saved connection profiles |
| `help` | Show help |

---

## 🧠 How It Works

```text
┌────────────────────┐
│  Detect backend    │
│  (nm / netplan /   │
│   snd / ifupdown)  │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  Suggest IP        │
│  based on current  │
│  CIDR + gateway    │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  Backup config     │
│  *.bak.<timestamp> │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  Apply + verify    │
│  (ping gw / WAN)   │
└────────────────────┘
```

- **NetworkManager** → modifies the per-SSID profile, so other Wi-Fi networks keep DHCP automatically.
- **netplan / systemd-networkd / ifupdown** → binds the static IP to the interface *for all networks*. A router-side DHCP reservation is usually preferable when roaming.

---

## ⚙️ Flags & Environment

| Flag / Variable | Effect |
|---|---|
| `--auto` | Non-interactive mode, accept suggested defaults |
| `--quiet`, `-q` | Disable spinner animation |
