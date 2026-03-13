# Visa CLI — Install Skill

You are installing Visa CLI, a payment infrastructure MCP server that gives AI agents the ability to pay for anything online. Cards, USDC stablecoins, x402 protocol, and Tempo pathUSD.

## What You Get

After installation, you will have 10 MCP tools for the full payment lifecycle:

- `checkout_pay` — Execute payments (card, USDC on Base, Tempo pathUSD). Auto-selects optimal rail.
- `checkout_get_status` — Check enrollment, wallet balances, spending limits.
- `checkout_get_cards` — List enrolled payment cards.
- `checkout_transaction_history` — View recent transactions and spend stats.
- `checkout_update_spending_controls` — Set per-transaction max, daily limit, auto-approve threshold.
- `checkout_add_card` — Enroll a card via secure VGS Collect form.
- `checkout_enroll_device` — Start Click to Pay enrollment (email or phone OTP).
- `checkout_verify_otp` — Complete enrollment with verification code.
- `checkout_login` — GitHub OAuth login + card enrollment flow.
- `checkout_reset` — Clear enrollment/credentials (wallet keys retained by default).

## Install Steps

Run these commands in order. Do not skip any step.

```bash
# 1. Clone the repo
git clone https://github.com/GannonCombs/Gannon-mcp.git

# 2. Build
cd Gannon-mcp
npm install
npm run build
npm link

# 3. Initialize (creates wallet and config)
visa-mcp init

# 4. Register with Claude Code
visa-mcp install claude
```

After step 4, restart Claude Code. The MCP server is now registered and all 10 tools are available.

## For OpenClaw

If you are running inside OpenClaw instead of Claude Code, replace step 4 with:

```bash
openclaw mcp add visa-mcp
```

## Verify Installation

After restarting, call `checkout_get_status` with no parameters. You should see:
- `enrolled`: true or false
- `base.address`: your USDC wallet address on Base
- Spending controls with default limits

If `checkout_get_status` is not available as a tool, the MCP server did not register correctly. Re-run step 4.

## Card Enrollment

To enroll a payment card after installation:

1. Call `checkout_add_card` (no parameters). This opens a secure VGS Collect form in the browser.
2. The human enters their card details in the form. Card data is tokenized by VGS (PCI DSS Level 1). No raw card numbers are stored.
3. Once submitted, the card is ready for payments.

For demo mode (no VGS credentials needed), run from terminal:
```bash
visa-mcp enroll-card --demo
```

## Funding Crypto Wallets

Call `checkout_get_status` to see wallet addresses, then:
- **Base wallet**: Send USDC on Base mainnet (chain 8453) to the `base.address`
- **Tempo wallet**: Send pathUSD on Tempo network (chain 42431) to the `tempo.address`

## Payment Rails

The server auto-selects the best rail based on merchant requirements, balance, and transaction size:

- **x402**: Pay x402-enabled HTTP endpoints with USDC on Base (GET and POST)
- **MPP / Tempo**: Pay via Tempo network using pathUSD stablecoins
- **MPP / Card**: Pay MPP endpoints with a VGS-vaulted card
- **Card (direct)**: Traditional card-not-present payments via VGS network tokenization

## What Agents Can Pay For

- x402 API endpoints (image gen, music gen, LLM inference, data feeds)
- Hosting and compute services
- Domain name registration
- Proprietary datasets and market data
- Any online merchant that accepts Visa

## Security

- Cards tokenized via VGS (PCI DSS Level 1). No raw PANs stored anywhere.
- Credentials stored in OS keychain, never in files.
- Biometric approval required for payments (Touch ID / Face ID / Windows Hello).
- Configurable spending limits: per-transaction max, daily limit, auto-approve threshold.

## Troubleshooting

**"visa-mcp: command not found"**
Run `npm link` again from inside the Gannon-mcp directory.

**"MCP server not available" after install**
Restart Claude Code or OpenClaw after running `visa-mcp install claude` or `openclaw mcp add visa-mcp`.

**"Insufficient USDC balance"**
Fund your Base wallet. Run `visa-mcp status` or call `checkout_get_status` to see wallet addresses.

**"No payment methods available"**
Enroll a card (`checkout_add_card`) or fund a crypto wallet.

## Links

- Website: https://visamcp.sh
- GitHub: https://github.com/GannonCombs/Gannon-mcp
- Agent reference: https://visamcp.sh/agent.txt
