# TradingHost + TradingView Webhooks

Deploy a webhook receiver that executes trades from [TradingView](https://www.tradingview.com) alerts on [TradingHost](https://tradinghost.com).

## What is this?

This repository is an AI-powered development scaffold. Use it as a template (click "Use this template" on GitHub), open your new repo in your AI coding tool (Cursor, Claude, Codex), and tell the AI what to build. The rules in `.cursor/rules/` teach the AI everything it needs to know about TradingView webhooks and TradingHost to help you create a production-quality alert execution system.

**The pattern:** TradingView fires an alert → sends HTTP POST to your TradingHost deployment → your code validates the request and executes the trade via CCXT on any supported exchange.

## Getting Started

1. **Click "Use this template"** → Create a new repository (you can make it private)
2. **Open in Cursor** (or your preferred AI coding tool)
3. **Tell the AI what to build** — e.g. "Build me a webhook receiver that executes TradingView alerts on Binance"
4. **Set credentials** — add `EXCHANGE_API_KEY`, `EXCHANGE_API_SECRET`, and `WEBHOOK_SECRET` as TradingHost strategy secrets; edit non-secret tunables (exchange, default symbol/amount, testnet) in `config.json`. Secrets are environment variables, never committed to git.
5. **Deploy on TradingHost** — link this repo as a strategy, create a deployment with **at least 1 allocated port**, and you're live
6. **Set up TradingView** — create an alert pointing at a port-443 HTTPS endpoint in front of your deployment (TradingView only delivers to ports 80/443, not the raw NodePort)

**Important:** This template requires a TradingHost deployment with at least 1 allocated port. The webhook server listens on this port for incoming TradingView alerts.

## How It Works

```
TradingView Alert → HTTP POST → TradingHost Deployment (your webhook server) → CCXT → Exchange
```

1. You create a TradingView alert with a webhook URL pointing to your deployment
2. When the alert fires, TradingView sends an HTTP POST with the alert data
3. Your webhook server validates the secret, parses the payload, and executes the trade
4. CCXT handles the exchange API interaction (Binance, Bybit, OKX, etc.)

## TradingHost Deployment

1. Create an account at [tradinghost.com](https://tradinghost.com)
2. Link this GitHub repository as a strategy
3. Create a deployment — **allocate at least 1 port** (required for the webhook server)
4. Note the deployment's external IP and NodePort from the deployment details (reachable via `http://<ip>:<nodePort>/webhook` for `curl`/custom callers)
5. **For TradingView:** it only sends to ports 80/443, so put a port-443 HTTPS endpoint (Cloudflare Tunnel, ngrok, or a reverse proxy) in front of the NodePort and use that `https://.../webhook` URL in your alert

## Documentation

- [TradingHost Docs](https://tradinghost.com/docs)
- [TradingView Alerts](https://www.tradingview.com/support/solutions/43000520149-about-alerts/)
- [CCXT Documentation](https://docs.ccxt.com)

## Risk Warning

Automated trade execution from alerts involves significant risk. A misconfigured alert can place unintended trades. Always test with testnet/paper trading first, use a webhook secret, and set reasonable position limits.
