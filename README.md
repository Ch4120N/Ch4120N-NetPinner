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
| `--no-color` | Disable ANSI colours |
| `NO_COLOR=1` | Same as `--no-color` |
| `NETPINNER_QUIET=1` | Same as `--quiet` |

---

## 📋 Examples

**Interactive setup on the current Wi-Fi**

```bash
sudo Ch4120N-NetPinner set
```

**Fully automated (great for provisioning)**

```bash
sudo Ch4120N-NetPinner set --auto --quiet
```

**Revert back to DHCP**

```bash
sudo Ch4120N-NetPinner revert
```

**Check what's going on**

```bash
Ch4120N-NetPinner status
```

**List every saved connection profile**

```bash
Ch4120N-NetPinner list
```

---

## 📱 Termux Notes

Android's networking stack (`netd`) does **not** allow changing the interface IP from a userspace shell — the OS will override any change. NetPinner therefore runs in **read-only mode** on Termux:

- ✅ Show Wi-Fi info via `termux-wifi-connectioninfo`
- ✅ Show current IPv4 / gateway
- ❌ Do **not** attempt to write `/system` or `/etc`

**Recommended approach on Android:** use your router's **DHCP reservation** feature to pin the device to a permanent IP by MAC address.

```bash
pkg install termux-api
# Install the Termux:API companion app from F-Droid
Ch4120N-NetPinner status
```

---

## 🛠️ Troubleshooting

<details>
<summary><strong>“This command requires root”</strong></summary>

Run with `sudo`. On systems where `sudo` isn't installed:

```bash
su -c 'Ch4120N-NetPinner set'
```
</details>

<details>
<summary><strong>“No active NetworkManager connection”</strong></summary>

Make sure Wi-Fi / Ethernet is actually connected:

```bash
nmcli connection show --active
```
</details>

<details>
<summary><strong>Gateway unreachable after applying</strong></summary>

The chosen IP may be outside your DHCP range or already taken. Revert and pick another:

```bash
sudo Ch4120N-NetPinner revert
sudo Ch4120N-NetPinner set
```
</details>

<details>
<summary><strong>netplan: PyYAML missing</strong></summary>

```bash
sudo apt install python3-yaml
```
NetPinner falls back to a standalone YAML file if PyYAML is unavailable, but merging into your existing netplan file is safer.
</details>

<details>
<summary><strong>Colours look broken</strong></summary>

```bash
Ch4120N-NetPinner --no-color status
# or
NO_COLOR=1 Ch4120N-NetPinner status
```
</details>

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

