# Trindr Demo

> ⚠️ We were unable to secure Adrena API access before the hackathon deadline, so this version ships with frontend-only integrations and public Solana/Dexscreener endpoints. If the Adrena credentials had arrived in time, we would have replaced the Dexter-sourced token data (Dexscreener token profiles + Gecko Terminal OHLCV) and the raw `api.mainnet-beta.solana.com` balance calls with Adrena’s authenticated APIs, and wired those calls through a small backend service to avoid leaking keys or rate-limiting tokens in the browser. The backend would also have safely emitted signed leaderboard events for Adrena’s infrastructure instead of trusting the client.

## Overview

Trindr is a “Tinder for Traders” browser experience built on Solana data. Players swipe through live Dexscreener profiles, buy with a swipe-right, sell with swipe-left, and track their SOL balance in the footer widget. A wallet gate ensures the interface remains locked until Phantom/Solflare connects, and we even hand out 1 SOL of demo credit when a wallet is empty so everyone can trial the flow.

Key features:

- Swipe-based trading UI with Spot and Leverage mode toggle.
- Real-time token enrichment via Dexscreener and Gecko Terminal OHLCV fetches.
- Solana wallet gating to fetch balances and simulate trades based on actual SOL holdings.
- Demo overlay that explains the experience and keeps interactions locked until a wallet is connected.

## Architecture (current)

- **Frontend only**: All logic runs in the browser. Token data fetches are made against `https://api.dexscreener.com/token-profiles/latest/v1` and `https://api.geckoterminal.com/api/v2/networks/{network}/pools/{pairAddress}/ohlcv/minute`.
- **Solana balance**: We call `https://api.mainnet-beta.solana.com` to read the wallet balance and compute the bulk of the leaderboard scoring.
- **Wallet gating**: A tiny wallet helper script detects Phantom/Solflare, persists the address, and enforces connect/disconnect behavior.
- **Demo credit**: Wallets reporting zero SOL still get a 1 SOL simulated balance for storytelling purposes.

## What would change with Adrena

Once Adrena provides API access, we would:

1. Replace the public Dexscreener/Gecko fetches with Adrena’s token feed endpoints to ensure the data matches the broader Adrena competitive ecosystem.
2. Route Solana balance queries (and any future order execution hooks) through an authenticated backend service that holds the Adrena API keys and signs every request with the user’s session credential.
3. Emit leaderboard updates, streaks, quest progress, and raffle entries from that same backend rather than from the client, ensuring every submission can be verified via Adrena’s signature scheme.
4. Tie in the Adrena leaderboard, quests, and raffles by posting events to their provided webhooks from the backend, giving us the same connectivity as the production product.

## Running locally / Deploying

1. Open `index.html` in any static host or GitHub Pages branch (already live at `trindr.github.io`).
2. Connect a Phantom or Solflare wallet via mobile’s in-app browser; the UI will request the SOL balance and unlock trades.
3. (Optional) Update RPC endpoints or overlay text by editing the constants near the top of the inline `<script>` block.

## Notes

- This version is intentionally frontend-focused because we lacked the backend credentials; the real competition build would add the backend layer to keep Adrena keys private and enable secured leaderboard submissions.
- Tests: manual browser checks only (wallet connection, swipe interactions, animated FX).
