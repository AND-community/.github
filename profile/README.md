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

*Ed25519 identity · libp2p network · terminal-native*

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

AND is a peer-to-peer forum community that runs in the terminal **without any server**.  
No accounts. No sign-up. No central infrastructure.

All you need is a terminal, the `and` binary and a password. On the first start AND creates your
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
| **Key-based identity** | 128-bit entropy (equivalent to a 12-word BIP-39 mnemonic) → Ed25519 key pair, stored encrypted with your password on your own device |
| **Signed forum** | Topics in 34 categories, published as signed GossipSub messages; every node verifies and stores them. Topics delete themselves after 5 days |
| **Public chat** | A signed P2P chat channel with rate limits and history sync |
| **Private messages and files** | Direct libp2p streams between the two peers; resumable file transfer that the recipient has to approve |
| **Personal sites** | Every user can publish a small multi-page site, signed and synced like the forum |
| **Moderation** | The founder issues signed moderator certificates; bans (1 hour to permanent), early topic deletion and an audit history |
| **Plugins** | Standalone binaries that talk to AND over a local HTTP API, each with its own key and the permissions the user approved |
| **Automatic updates** | From GitHub Releases, applied only if the checksum file carries the project's Ed25519 signature |

---

## Repositories

| Repository | What it is |
|---|---|
| [**AND**](https://github.com/AND-community/AND) | The application itself, plus the Admin Panel, Özel Chat (private chat), Site and Example plugins and the VS Code extension (`and-vscode`) |
| [**konu_ac**](https://github.com/AND-community/konu_ac) | The new-topic plugin — needed to create forum topics |
| [**DeltaX**](https://github.com/AND-community/DeltaX) | Private groups with their own version control system, synced end-to-end encrypted between the members |
| [**Pembis**](https://github.com/AND-community/Pembis) | A theme plugin that paints AND pink |
| [**and-arama-motoru**](https://github.com/AND-community/and-arama-motoru) | The AND Search Engine: an independent, read-only peer that indexes the network's content and searches it from the terminal |
| [ozel_chat](https://github.com/AND-community/ozel_chat) | *Archived.* Özel Chat now lives in the AND repository (`Eklentiler/ozel_chat`) |

---

## Getting started

Building from source needs Go 1.26.4 or later (older Go versions from 1.21 on download the right
toolchain by themselves):

```bash
git clone https://github.com/AND-community/AND.git
cd AND
go build -o and ./cmd/and

# Plugins are built into their own folders next to their plugin.json
go build -o Eklentiler/ozel_chat/and-plugin-ozel_chat ./Eklentiler/ozel_chat

# Konu Aç (needed to create topics) lives in its own repository
git clone https://github.com/AND-community/konu_ac.git Eklentiler/konu_ac
go build -o Eklentiler/konu_ac/and-plugin-konu_ac ./Eklentiler/konu_ac

./and
```

On Windows add `.exe` to the binary names. The full instructions — all plugins, ldflags, the
founder key — are in the [AND README](https://github.com/AND-community/AND#installation).

---

## Plugin system

AND plugins are separate programs. AND finds each one in `Eklentiler/<name>/` next to its own binary:
a `plugin.json` manifest plus an `and-plugin-<name>` binary. When you open a plugin, AND asks once
for the permissions it declares, then starts it in the same terminal and passes it the address of a
local HTTP API and a key of its own. When the plugin exits, AND takes the terminal back.

```json
{
  "name":        "example",
  "label":       "Example Plugin",
  "version":     "1.0.0",
  "description": "Short description",
  "author":      "Your name",
  "permissions": ["dm"]
}
```

A plugin with `"label": ""` doesn't appear in the menu (konu_ac is opened from the forum with `n`).
Plugins open in AND's interface language. A full template and the API reference are in
[Eklentiler/ornek](https://github.com/AND-community/AND/tree/main/Eklentiler/ornek) (in Turkish).

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

Terminal safety
  Text from the network is cleaned of escape sequences and control
  characters before it is drawn, so it can't take over your terminal.

Plugins
  Each plugin gets its own API key and only the permissions you approved.

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
