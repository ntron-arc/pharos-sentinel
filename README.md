# pharos-sentinel 👁

**The AI-native onchain threat intelligence layer for Pharos Network.**

Every other skill waits for your command. **Sentinel watches without being asked.**

---

## What Is This?

Pharos Sentinel is an AI Agent skill that gives any agent (Claude Code, OpenClaw, Codex) a proactive security intelligence capability on Pharos Network.

Instead of executing transactions, Sentinel **reads, reasons, and warns** — combining live onchain data from the Pharos RPC with Claude AI analysis to deliver a plain-English threat assessment of any wallet address.

**This is the only Pharos skill submission combining:**
- ✅ Live RPC data fetching
- ✅ Multi-layer automated threat scoring
- ✅ Claude AI reasoning on real blockchain data
- ✅ Unlimited approval detection
- ✅ Contract vs EOA detection
- ✅ Live interactive demo (no install needed)

---

## Live Demo

👉 **[https://ntron-arc.github.io/pharos-sentinel/demo/](https://ntron-arc.github.io/pharos-sentinel/demo/)**

Paste any Pharos wallet address. Watch Sentinel:
1. Connect to the Pharos RPC
2. Fetch live onchain data
3. Run threat analysis
4. Call Claude AI for an intelligence briefing — in real time

No wallet connection needed. No private key. 100% read-only.

---

## The Problem Nobody Else Solved

Every other Agent Centre submission executes transactions — swap, stake, deploy, airdrop.

But **before you execute anything, you need to know if it's safe.**

- Is the recipient address a contract or an EOA?
- Do you have unlimited token approvals sitting open?
- Is your balance critically low?
- Has this wallet shown suspicious patterns?

Sentinel answers all of this **before you act.** It also runs as a continuous monitor — watching a wallet and alerting when anything changes.

---

## Installation

```bash
# Clone the repo
git clone https://github.com/ntron-arc/pharos-sentinel

# Copy SKILL.md to your agent's skills directory
cp pharos-sentinel/SKILL.md ~/.claude/skills/        # Claude Code
cp pharos-sentinel/SKILL.md ~/.openclaw/skills/      # OpenClaw
cp pharos-sentinel/SKILL.md ~/.codex/skills/         # Codex
```

> Requires: `cast` (Foundry) + `pharos-skill-engine` as base dependency.
> Install Foundry: `curl -L https://foundry.paradigm.xyz | bash`

---

## Usage

### Security Scan
```
"Is my wallet safe? 0x742d35Cc..."
"Scan this address before I send funds: 0x..."
"Check for threats on my Pharos wallet"
"Audit 0x... on Pharos testnet"
```

### Due Diligence Before Sending
```
"Is it safe to send 5 PHRS to 0x...?"
"Verify this recipient before I transact"
"Is this a contract or a real wallet?"
```

### Monitoring Mode
```
"Watch my wallet 0x... and alert me of any changes"
"Monitor this Pharos address continuously"
```

### Integrated Pre-flight Check
```
"Swap 5 PHRS — but check my wallet first"
"Run a security check then stake my PHRS"
```

---

## How Threat Scoring Works

| Flag | Severity | Score |
|---|---|---|
| Smart contract at address | CRITICAL | +30 |
| Unlimited token approval | CRITICAL | +35 |
| Zero native balance | HIGH | +20 |
| High-value wallet (>100 PHRS) | HIGH | +18 |
| Zero transaction history | MEDIUM | +15 |
| Balance < 0.01 PHRS | MEDIUM | +12 |
| Large stablecoin exposure | MEDIUM | +10 |
| Deep tx history (>50 txs) | POSITIVE | -8 |
| Active in last 7 days | POSITIVE | -10 |

**0–25 = 🟢 LOW · 26–50 = 🟡 MODERATE · 51–75 = 🟠 HIGH · 76–100 = 🔴 CRITICAL**

---

## Repository Structure

```
pharos-sentinel/
├── SKILL.md                     ← Agent skill definition (install this)
├── README.md                    ← This file
├── assets/
│   ├── tokens.json              ← Known ERC-20 addresses on Pharos
│   ├── contracts.json           ← Known Pharos ecosystem contracts
│   └── threat-signatures.json  ← Threat pattern signatures
└── demo/
    └── index.html               ← Live AI-powered demo (open in browser)
```

---

## Supported Frameworks

| Framework | Status |
|---|---|
| Claude Code | ✅ Full support |
| OpenClaw | ✅ Full support |
| Codex | ✅ Full support |
| Any `.md` skill-compatible agent | ✅ |

---

## Dependencies

- `cast` — Foundry CLI (`curl -L https://foundry.paradigm.xyz | bash`)
- `pharos-skill-engine` v0.1.0+ (base dependency)
- Claude AI API (for intelligence briefing — optional, threat flags work without it)

---

## Security Design

Sentinel is built on a zero-trust, read-only model:

- **No private key required** — ever
- **No wallet connection** in the demo
- **All data from Pharos RPC directly** — no third-party indexers
- **Ephemeral results** — nothing stored or logged
- **AI explains, you decide** — Claude surfaces intelligence, you make the call

---

## License

MIT-0 — Free to use, modify, redistribute. No attribution required.

---

Built for the **Pharos Agent Centre Skill Builder Campaign** · June 2026

*Pharos Sentinel — Because onchain security shouldn't wait to be asked.*
