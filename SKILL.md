---
name: shieldbot-security
description: BNB Chain security scanner — scan contracts, simulate transactions, detect phishing, investigate deployers, track scam campaigns, and audit wallet approvals via ShieldBot's API.
metadata: { "openclaw": { "emoji": "🛡️", "requires": { "bins": ["python"] }, "os": ["win32", "linux", "darwin"] } }
---

# ShieldBot Security Skill

Autonomous transaction firewall for BNB Chain and 6 EVM networks. Scan contracts, simulate transactions before signing, investigate deployers, detect phishing, audit wallet approvals, and monitor live threats.

**Base URL:** https://api.shieldbotsecurity.online — No API key required.

## Trigger Keywords

Activate this skill when the user says any of: `scan`, `simulate`, `firewall`, `deployer`, `threats`, `phishing`, `check url`, `scammers`, `campaigns`, `approvals`, `rescue`, `shieldbot`, or `ask shieldbot`.

## Commands

When the user triggers a command, run the corresponding shell command. Replace `{baseDir}` with the skill's install directory.

### scan — Contract Security Scan

User says: "scan 0x..." or "is this contract safe" + address

```bash
python "{baseDir}/shieldbot_client.py" --action scan --address ADDRESS --chain CHAIN_ID
```

Default chain: 56 (BSC). Always ask for the address if not provided.

### simulate — Transaction Firewall

User says: "simulate this transaction" or "is this tx safe" + params

```bash
python "{baseDir}/shieldbot_client.py" --action simulate --to TO_ADDRESS --from-addr FROM_ADDRESS --value VALUE_HEX --data CALLDATA_HEX --chain CHAIN_ID
```

All params required. `--value` defaults to 0x0, `--data` defaults to 0x.

If the result shows `classification: BLOCK_RECOMMENDED`, warn the user immediately. Do NOT advise proceeding.

### deployer — Deployer Investigation

User says: "deployer 0x..." or "who deployed this" or "check deployer"

```bash
python "{baseDir}/shieldbot_client.py" --action deployer --address ADDRESS --chain CHAIN_ID
```

A deployer with `campaign.severity: HIGH` and multiple high-risk contracts is a serial scammer. Warn even if the current token scan looks clean.

### threats — Live Threat Feed

User says: "threats" or "what threats are active" or "latest scams"

```bash
python "{baseDir}/shieldbot_client.py" --action threats --limit 10 --chain CHAIN_ID
```

### check — Phishing URL Detection

User says: "check [url]" or "is this site safe" or "phishing check"

```bash
python "{baseDir}/shieldbot_client.py" --action phishing --url "URL"
```

If `is_phishing: true`, warn: "Do NOT connect your wallet to this site."

### scammers — Top Scam Campaigns

User says: "scammers" or "top scam deployers" or "active campaigns"

```bash
python "{baseDir}/shieldbot_client.py" --action campaigns --limit 10
```

### approvals — Wallet Approval Audit

User says: "approvals 0x..." or "check my approvals" or "am I exposed"

```bash
python "{baseDir}/shieldbot_client.py" --action approvals --address WALLET_ADDRESS --chain CHAIN_ID
```

For each HIGH risk approval, explain what the spender can do and recommend revoking.

### ask — AI Security Advisor

User says: "ask shieldbot [question]" or any DeFi security question

```bash
python "{baseDir}/shieldbot_client.py" --action ask --message "USER_QUESTION" --chain CHAIN_ID
```

## Response Rules

1. Wrap all command output in a markdown code block (triple backticks).
2. Lead with the verdict or classification — never bury the risk score.
3. If honeypot is detected, say it first regardless of anything else.
4. Always note that automated scans are not financial advice.
5. Default to BSC (chain 56) if the user does not specify a chain.

## Supported Chains

| Chain | chainId |
|-------|---------|
| BNB Smart Chain | 56 |
| Ethereum | 1 |
| Base | 8453 |
| Arbitrum One | 42161 |
| Polygon | 137 |
| Optimism | 10 |
| opBNB | 204 |

## Risk Score Guide

- 0-20: SAFE — No issues detected
- 21-40: LOW RISK — Minor concerns
- 41-60: MEDIUM RISK — Proceed with caution
- 61-80: HIGH RISK — Significant red flags, recommend avoiding
- 81-100: CRITICAL — Almost certainly a scam, do NOT interact

## Environment

Override the API base URL by setting `SHIELDBOT_API_BASE` environment variable.
