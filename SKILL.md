---
name: pharos-sentinel
description: The AI-native onchain guardian for Pharos Network. Invoke when any user wants to monitor, audit, or get a threat assessment on a Pharos wallet address. Unlike every other skill that waits for a command, Sentinel proactively watches — fetching live onchain data, running multi-layer threat analysis, detecting anomalies, and delivering a Claude AI intelligence briefing in plain English. Trigger on phrases like "watch my wallet", "is my wallet safe", "monitor this address", "threat analysis", "anything suspicious", "scan my Pharos wallet", "check for risks", "onchain security check", "is this address safe to send to", "audit this wallet", "what's wrong with my wallet", or any security/monitoring intent related to a Pharos address. This is the ONLY Pharos skill combining live RPC data + AI reasoning into a single threat report. Requires no private key for analysis.
version: 1.0.0
requires:
  anyBins:
    - cast
  skills:
    - pharos-skill-engine
---

# Pharos Sentinel — AI Threat Intelligence Layer

Every other skill waits for you to ask.
**Sentinel watches without being asked.**

This skill gives AI agents a proactive security and monitoring capability on Pharos Network — combining live onchain data fetching via `cast`, multi-layer threat analysis, and Claude AI reasoning to deliver actionable wallet intelligence in plain English.

---

## What Sentinel Does Differently

| Standard Skills | Pharos Sentinel |
|---|---|
| Wait for explicit commands | Proactively analyzes without being prompted |
| Return raw data | Returns AI-interpreted threat assessments |
| Single operation | Multi-layer analysis in one run |
| Require private key | 100% read-only, no key needed |
| Show numbers | Explain what the numbers MEAN |

---

## Trigger Recognition

The agent invokes this skill for ANY of these intents:

```
Security / Monitoring:
  "Is my wallet safe?"
  "Scan this address for threats"
  "Anything suspicious on this wallet?"
  "Check this address before I send funds"
  "Audit my Pharos wallet"
  "What's the risk level of this wallet?"

Monitoring:
  "Watch wallet 0x..."
  "Monitor my Pharos address"
  "Alert me if anything changes"
  "Is this address active?"

Due Diligence:
  "Is it safe to send to 0x...?"
  "Verify this address"
  "Is this a contract or a wallet?"
  "Check if this is a scam address"
```

---

## Execution Protocol

### Phase 1 — Onchain Data Collection (read-only)

```bash
# 1. Native balance
cast balance <ADDRESS> --rpc-url $RPC_URL --ether

# 2. Transaction count (activity depth)
cast tx-count <ADDRESS> --rpc-url $RPC_URL

# 3. Code check — is this a contract or EOA?
cast code <ADDRESS> --rpc-url $RPC_URL
# If result != "0x" → CONTRACT DETECTED → flag immediately

# 4. Latest block (recency context)
cast block-number --rpc-url $RPC_URL

# 5. ERC-20 token balances (loop known tokens)
cast call <TOKEN> "balanceOf(address)(uint256)" <ADDRESS> --rpc-url $RPC_URL

# 6. Check token approvals (unlimited approval detection)
cast call <TOKEN> "allowance(address,address)(uint256)" <ADDRESS> <KNOWN_SPENDER> --rpc-url $RPC_URL
# Flag if allowance == type(uint256).max (115792...ffe)
```

### Phase 2 — Threat Scoring

The agent calculates a threat score 0–100 using this matrix:

| Condition | Severity | Score |
|---|---|---|
| Smart contract address (not EOA) | CRITICAL | +30 |
| Zero native balance | HIGH | +20 |
| Balance < 0.01 PHRS | MEDIUM | +12 |
| Zero transaction history | MEDIUM | +15 |
| Unlimited token approval detected | CRITICAL | +35 |
| Balance > 100 PHRS (high-value target) | HIGH | +18 |
| Large stablecoin exposure (>$1000) | MEDIUM | +10 |
| Recently active (last 7 days) | POSITIVE | -10 |
| Deep tx history (>50 txs) | POSITIVE | -8 |

**Score Interpretation:**
```
0–25:   🟢 LOW RISK — Wallet is healthy
26–50:  🟡 MODERATE — Attention recommended
51–75:  🟠 HIGH RISK — Action required
76–100: 🔴 CRITICAL — Immediate action needed
```

### Phase 3 — AI Threat Briefing

After data collection and scoring, the agent calls Claude AI with all collected data and generates a 4-paragraph intelligence briefing:

```
Paragraph 1: Overall threat assessment with score context
Paragraph 2: Specific risks identified (with actual numbers)
Paragraph 3: Immediate actions the wallet owner should take
Paragraph 4: Forward-looking monitoring recommendation
```

### Phase 4 — Output Format

```
══════════════════════════════════════════════════
  PHAROS SENTINEL — THREAT INTELLIGENCE REPORT
  Address: 0x...
  Network: [Network Name]
  Scanned: [timestamp] · Block #[latest]
══════════════════════════════════════════════════

🔴 THREAT SCORE: XX/100 — [CRITICAL/HIGH/MODERATE/LOW]

THREAT FLAGS:
  [CRITICAL] Smart Contract Address Detected
             This is not an EOA. Direct PHRS transfers may be lost.

  [HIGH]     Zero Native Balance
             Cannot pay gas. All operations will fail.

  [MEDIUM]   Low Transaction History
             Only 3 transactions. Possible dust/airdrop farm address.

  [LOW]      Recently Active
             Last activity 2 days ago. Wallet appears operational.

TOKEN EXPOSURE:
  USDC:  245.50
  USDT:  0.00
  WPHRS: 1.2300

UNLIMITED APPROVALS DETECTED:
  [NONE FOUND] ✅

══════════════════════════════════════════════════
  CLAUDE AI INTELLIGENCE BRIEFING
══════════════════════════════════════════════════

[Full Claude AI threat analysis paragraph here]

══════════════════════════════════════════════════
  RECOMMENDED ACTIONS
══════════════════════════════════════════════════
  1. [Specific action based on highest severity flag]
  2. [Second action]
  3. [Monitoring recommendation]

══════════════════════════════════════════════════
```

---

## Unlimited Approval Detection

This is the most dangerous and underdetected vulnerability in DeFi. Sentinel checks all known spender contracts:

```bash
# Check if TOKEN has unlimited approval for SPENDER
ALLOWANCE=$(cast call $TOKEN \
  "allowance(address,address)(uint256)" \
  $WALLET $SPENDER \
  --rpc-url $RPC_URL)

# Compare to max uint256
MAX_UINT="115792089237316195423570985008687907853269984665640564039457584007913129639935"

if [ "$ALLOWANCE" = "$MAX_UINT" ]; then
  echo "⚠ CRITICAL: Unlimited approval detected for $TOKEN → $SPENDER"
  echo "Recommended: cast send $TOKEN 'approve(address,uint256)' $SPENDER 0 --rpc-url $RPC_URL --private-key $KEY"
fi
```

---

## Monitoring Mode

When the user asks to "watch" or "monitor" an address, the agent sets up a polling loop:

```bash
# Poll every 60 seconds
while true; do
  NEW_TX=$(cast tx-count <ADDRESS> --rpc-url $RPC_URL)
  NEW_BAL=$(cast balance <ADDRESS> --rpc-url $RPC_URL --ether)

  if [ "$NEW_TX" != "$PREV_TX" ]; then
    echo "⚡ NEW TRANSACTION DETECTED on <ADDRESS>"
    echo "   Previous tx count: $PREV_TX → Now: $NEW_TX"
    echo "   Balance: $PREV_BAL → $NEW_BAL PHRS"
    # Trigger re-analysis
  fi

  PREV_TX=$NEW_TX
  PREV_BAL=$NEW_BAL
  sleep 60
done
```

---

## Due Diligence Mode

Before the user sends funds to an unknown address:

```bash
# Validate recipient before sending
cast code <RECIPIENT> --rpc-url $RPC_URL
# → "0x" = safe EOA
# → anything else = CONTRACT (verify ABI before sending)

cast tx-count <RECIPIENT> --rpc-url $RPC_URL
# → 0 = fresh/unverified address (warn user)
# → >10 = established address (lower risk)

cast balance <RECIPIENT> --rpc-url $RPC_URL --ether
# → 0 = possibly dormant/wrong address
```

---

## Security Principles

- **Zero private key requirement** — all operations are read-only
- **No data stored** — scan results are ephemeral
- **No third-party services** — all data comes directly from Pharos RPC
- **Transparent scoring** — every flag shows its exact reasoning
- **AI explains, not decides** — Claude surfaces intelligence, user makes final calls

---

## Integration with Other Skills

Sentinel works best as a pre-flight check before other skills execute:

```
User: "Swap 5 PHRS on Pharos"
Agent: [Runs Sentinel check first]
       → Score: 8/100 (LOW RISK)
       → No unlimited approvals
       → Sufficient balance
       → Proceeding with swap...

User: "Send 10 PHRS to 0x1234..."
Agent: [Sentinel scans recipient]
       → Contract detected at recipient
       → ⚠ WARNING: Recipient is a smart contract
       → Confirm before proceeding?
```

---

## Asset Files

```
pharos-sentinel/
├── SKILL.md                     ← this file
├── assets/
│   ├── tokens.json              ← ERC-20 token addresses on Pharos
│   ├── contracts.json           ← Known spender/protocol contracts
│   └── threat-signatures.json  ← Known malicious patterns
├── demo/
│   └── index.html               ← Live AI-powered demo
└── README.md
```

---

## Error Handling

| Scenario | Response |
|---|---|
| Address is invalid format | Reject with format guidance before any RPC call |
| RPC connection fails | Retry once with backup endpoint, then report connectivity issue |
| Token contract reverts | Skip that token, note in report |
| Claude API unavailable | Deliver raw threat flags without AI narrative |
| Zero transactions | Report as unverified address, do not assume malicious |
