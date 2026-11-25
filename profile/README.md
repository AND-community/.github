<div align="center">

```
 █████╗ ███╗   ██╗██████╗
██╔══██╗████╗  ██║██╔══██╗
███████║██╔██╗ ██║██║  ██║
██╔══██║██║╚██╗██║██║  ██║
██║  ██║██║ ╚████║██████╔╝
╚═╝  ╚═╝╚═╝  ╚═══╝╚═════╝
```

**Pseudonymous · Decentralized · Lightly moderated**

*Ed25519 identity · libp2p network · desktop app and terminal*

[**and-community.github.io**](https://and-community.github.io/)

**English** · [Türkçe](https://github.com/AND-community/.github/blob/main/profile/README.tr.md)

---

![Go](https://img.shields.io/badge/Go-1.26.4%2B-00ADD8?style=flat-square&logo=go&logoColor=white)
![libp2p](https://img.shields.io/badge/network-libp2p-7B2FBE?style=flat-square)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20Windows-555555?style=flat-square)
![License](https://img.shields.io/badge/license-AGPL--3.0-22863a?style=flat-square)
![Status](https://img.shields.io/badge/status-active%20development-orange?style=flat-square)

</div>

---

## What is AND?

AND is a peer-to-peer forum community that runs **without any server**, in its own desktop window or in the terminal.  
No accounts. No sign-up. No central infrastructure.

All you need is the `and` program and a password. On the first start AND creates your
identity — an Ed25519 key pair — and shows it once as a 32-character **recovery code**. That code is
your identity: with it you can restore your identity on another computer or if you forget your
password; without it there is no way back.

Every node is both a client and a server. Topics, replies and chat messages are signed and spread
over libp2p GossipSub; each node keeps its own copy in a local SQLite database. The interface is
available in six languages: Turkish, English, German, Spanish, French and Russian.

---

## Key features

| Feature | Description |
|---|---|
| **No servers** | Peer discovery through the libp2p DHT and mDNS; no central infrastructure to rely on |
| **Desktop app** | The default interface: AND's own window (WebView2 on Windows, the default browser on Linux and macOS) with a sidebar, Ctrl+K quick access and the plugins inside it. `and --terminal` opens the full-screen terminal interface |
| **Key-based identity** | 128-bit entropy (equivalent to a 12-word BIP-39 mnemonic) → Ed25519 key pair, stored encrypted with your password on your own device |
| **Signed forum** | Topics in 34 categories, published as signed GossipSub messages; every node verifies and stores them. Topics delete themselves after 5 days |
| **Public chat** | A signed P2P chat channel with rate limits and history sync |
| **Private messages and files** | Direct libp2p streams between the two peers; resumable file transfer that the recipient has to approve |
| **Personal sites** | Every user can publish a small multi-page site, signed and synced like the forum |
| **Moderation** | The founder issues signed moderator certificates; bans (1 hour to permanent), early topic deletion and an audit history |
| **Plugins** | Standalone programs that talk to AND over a local HTTP API, each with its own key and the permissions the user approved. In the desktop app they run inside AND's window, grouped by category; the bundled ones draw their screens with AND's own components. Install with `and plugin install`, start your own with `and plugin new` |
| **Automatic updates** | From GitHub Releases, applied only if the checksum file carries the project's Ed25519 signature |

---

## Repositories

| Repository | What it is |
|---|---|
| [**AND**](https://github.com/AND-community/AND) | The application itself, plus the Forum, Admin Panel, Özel Chat (private chat), Site and Example plugins and the VS Code extension (`and-vscode`) |
| [**DeltaX**](https://github.com/AND-community/DeltaX) | Private groups with their own version control system, synced end-to-end encrypted between the members |
| [**Pembis**](https://github.com/AND-community/Pembis) | A theme plugin that paints AND pink |
| [**and-arama-motoru**](https://github.com/AND-community/and-arama-motoru) | The AND Search Engine: an independent, read-only peer that indexes the network's content and searches it from the terminal |
| [**and-community.github.io**](https://github.com/AND-community/and-community.github.io) | The website at [and-community.github.io](https://and-community.github.io/), in English and Turkish |
| [ozel_chat](https://github.com/AND-community/ozel_chat) | *Archived.* Özel Chat now lives in the AND repository (`Eklentiler/ozel_chat`) |
| [konu_ac](https://github.com/AND-community/konu_ac) | *Archived.* The new-topic form is now part of AND's Forum plugin (`Eklentiler/forum`) |

---

## Getting started

Building from source needs Go 1.26.4 or later (older Go versions from 1.21 on download the right
toolchain by themselves):

```bash
git clone https://github.com/AND-community/AND.git
cd AND
go build -o and ./cmd/and

# Plugins are built into their own folders next to their plugin.json;
# the Forum plugin draws the forum
go build -o Eklentiler/forum/and-plugin-forum ./Eklentiler/forum
go build -o Eklentiler/ozel_chat/and-plugin-ozel_chat ./Eklentiler/ozel_chat

./and
```

On Windows add `.exe` to the binary names. The full instructions — all plugins, ldflags, the
founder key — are in the [AND README](https://github.com/AND-community/AND#installation).

---

## Plugin system

AND plugins are separate programs. AND finds each one in `Eklentiler/<name>/` next to its own binary
(plugins installed with `and plugin install` go into the data directory): a `plugin.json` manifest
plus an `and-plugin-<name>` binary. When you open a plugin, AND asks once for the permissions it
declares, then starts it with the address of a local HTTP API and a key of its own.

In the desktop app plugins run inside AND's window, grouped in the sidebar by their `category`. A
plugin that declares `"ui"` sends its screens to AND as data, and AND checks them and draws them with
its own components, so they look like the rest of the app. Other plugins are terminal programs; on
Windows they run in a terminal inside the window. In the terminal interface a plugin takes over the
terminal until it exits.

```json
{
  "name":        "example",
  "label":       "Example Plugin",
  "version":     "1.0.0",
  "description": "Short description",
  "author":      "Your name",
  "category":    "tools",
  "permissions": ["dm"],
  "ui":          1
}
```

A plugin with `"label": ""` doesn't appear in the menu. Plugins open in AND's interface language.
`and plugin new <name>` writes a working plugin project with tests and translations; the full guide
is [PLUGINS.md](https://github.com/AND-community/AND/blob/main/PLUGINS.md).

---

## Security model

```
Identity
  128-bit entropy → Ed25519 private key, kept on your device,
  encrypted with your password (Argon2id + AES-256-GCM).
  Unsigned messages are rejected by the network.

Moderation
  The founder signs moderator certificates with Ed25519.
  Bans and deletions are signed too; every node verifies them on its own.
  A revoked certificate stops working immediately.

Messages
  Private messages travel over a direct, encrypted libp2p stream between
  the two peers; the local history is encrypted with a key derived from
  your identity.

Interface
  The desktop interface is served only on 127.0.0.1, to a window that
  opens with a one-time code, under a strict Content Security Policy;
  network content is never treated as HTML. In the terminal, text from
  the network is cleaned of escape sequences and control characters, so
  it can't take over your terminal.

Plugins
  Each plugin gets its own API key and only the permissions you approved.
  AND checks the screens a plugin sends before drawing them.

Never share
  identity.dat   — your encrypted private key
  founder.key    — the founder key
  bans/          — moderation records
```

To report a vulnerability, follow [SECURITY.md](https://github.com/AND-community/AND/blob/main/SECURITY.md)
— please don't open a public issue.

---

## Tech stack

| Layer | Library |
|---|---|
| P2P network | `go-libp2p` — DHT (`go-libp2p-kad-dht`), mDNS, GossipSub (`go-libp2p-pubsub`), streams |
| Desktop | A WebView2 window through `wailsapp/go-webview2` on Windows (the default browser elsewhere), served by a local Go HTTP server; `xterm.js` for terminal plugins |
| TUI | `bubbletea` + `lipgloss` + `bubbles` |
| Local store | `modernc.org/sqlite` (pure Go, no CGO) |
| Identity | Ed25519 (Go standard library) + `tyler-smith/go-bip39` |
| Updates | GitHub Releases API + SHA-256 checksums signed with Ed25519 |
| Tests | Go's `testing` package, fuzz tests, end-to-end tests with two nodes |

---

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/feature-name`
3. Commit your changes
4. Open a pull request

The contribution guide is in [CONTRIBUTING.md](https://github.com/AND-community/AND/blob/main/CONTRIBUTING.md) (in Turkish).

---

<div align="center">

*AND is written entirely in Go and licensed under the AGPL-3.0.*  
*You don't have to trust a server.*

</div>
