# ⚡ SatsKids

**A Bitcoin savings app for families. Teach kids about Bitcoin, saving, and the value of money — through weekly tasks, savings goals, and Lightning payments.**

---

**Live app:** [https://Bitcoin-Lebowski.github.io/satskids/](https://Bitcoin-Lebowski.github.io/satskids/)
**Source:** [github.com/Bitcoin-Lebowski/satskids](https://github.com/Bitcoin-Lebowski/satskids)

> ⚠️ **IMPORTANT: Real Lightning payments are NOT functional in the current version.** All sats are simulated. The settings screen accepts LNbits connection details but the actual payment flow is not yet wired up. Do not expect real Bitcoin to move. See the [Connecting real Lightning payments](#connecting-real-lightning-payments-advanced) section for full details of what is and isn't built.

---

## What is it?

SatsKids is a single HTML file that runs entirely in your browser — no installation, no server, no account, no ongoing cost. Everything is stored locally on your device.

Kids tick off their tasks during the week. On Saturday morning (or whenever works for your family), a parent reviews what's been completed, generates a Lightning invoice, and pays it. The kids watch their sat balance grow, set savings goals, learn about Bitcoin, and see the real-world value of their stack update live.

**By default, sats are simulated** — the balances are real numbers but not backed by actual Bitcoin. This is intentional: it lets families learn the habits and concepts without needing to handle real money. When you're ready, you can connect a real Lightning backend (see below).

---

## Getting started

1. Save `index.html` anywhere on your computer
2. Open it in any modern browser (Chrome, Brave, Firefox, Safari)
3. On first launch, the onboarding walkthrough will guide you through the setup
4. Turn on **Parent mode** — you'll be prompted to set a 4-digit PIN
5. Add your kids with the **+** button
6. Add their weekly tasks and sat amounts in parent mode
7. That's it

No Wi-Fi needed to use the app (though the live BTC price ticker requires an internet connection).

---

## Features

### For kids
- Personal wallet with sat balance and live £ equivalent
- Week view — Mon–Sun grid showing daily and weekly tasks, tick off completions each day
- Tasks tab — see all tasks, schedule, and this week's progress at a glance
- Savings goals — enter cost in GBP, USD, EUR, THB, or sats directly, converts at live price
- "How many weeks to reach my goal?" calculator on the Goals tab
- Transaction history
- Bitcoin Stacking tracker — current value vs when first earned, gain/loss, future projections
- Spend tracker — record purchases and deduct from balance
- Streak counter with animated flame that grows with consecutive weeks
- Badges for milestones

### For parents
- PIN-protected parent mode
- Add, edit and delete tasks per child — in parent mode only
- Daily tasks (priced per day completed) and weekly tasks (done or not done)
- Task scheduling — every day, weekdays only, or custom days
- Recurring tasks — survive weekly reset automatically
- Optional 2× challenge — kids can opt in for double sats or nothing on daily tasks
- Copy a task to other kids in one tap when creating
- Weekly review wizard — guided Sunday flow, per-child correction, payment, challenge opt-in for next week
- Invoice flow — generates a fake Lightning invoice QR, then triggers the payment animation
- Parent demo wallet (10,000,000 sats) showing live conversions and price scenarios
- Export and import backup (JSON file)

### Educational
**Bitcoin School** — seven expandable lessons, each with animations:
- What is Bitcoin?
- What are sats?
- Why is Bitcoin special? (sound money, 21 million cap)
- How does Lightning work?
- Why does the price change?
- What is the Bitcoin Halving? (with live countdown to next halving)
- What is Bitcoin mining?
- Why save instead of spend?

### Sounds & animations
- Ascending chime when an invoice is paid
- Descending whoosh when sats are spent
- Soft click when ticking a task
- Lightning bolt and sat-rain animation on payment
- Money-flying animation on spend

---

## Data & privacy

Everything is stored in your browser's `localStorage`. Nothing is sent anywhere. No accounts, no tracking, no analytics.

**Important:** localStorage is tied to the browser and device. If you clear your browser data, your SatsKids data will be lost. Use **Settings → Export backup** regularly to save a JSON file you can restore from.

---

## Connecting real Lightning payments (advanced)

> ⛔ **THIS DOES NOT WORK YET.**
>
> The settings screen lets you enter an LNbits URL and API key, and the "Test connection" button will verify it reaches your server. That is all. When you tap "Pay invoice" in the app, it runs a simulated animation regardless of whether a backend is configured. No real sats move. No real invoice is generated. No real payment is made.
>
> This section documents what needs to be built and how the intended architecture works, for developers who want to contribute that functionality.

SatsKids is designed to connect to **LNbits** as a Lightning backend, which gives each child a real isolated wallet backed by actual Bitcoin.

### What you'll need
- An **LNbits instance** — either self-hosted or via a hosted provider
- A separate **LNbits wallet** for each child, each with its own API key
- Your own Lightning funding source behind LNbits (e.g. [Phoenixd](https://phoenix.acinq.co/server), [Alby Hub](https://albyhub.com/), or [Blink](https://www.blink.sv/))

### Recommended setup (no node required)
The simplest real-money setup:

1. Run **Phoenixd** on your desktop — it handles Lightning channels automatically with no manual management. You only need to start it on Saturday mornings when you pay
2. Connect **LNbits** to Phoenixd via its local API
3. Create one LNbits wallet per child in your LNbits dashboard
4. Enter each child's LNbits wallet invoice key in SatsKids Settings

### Security notes for real sats
If you use real Lightning payments, please be aware:

- **The PIN is stored in plaintext in localStorage.** For real money, you should treat the device as the security boundary — don't share the device with untrusted people
- **Store invoice-only keys, not admin keys.** LNbits gives you both — the invoice key can only receive, not spend. Use that one in SatsKids
- **Back up regularly.** Use Settings → Export backup before every session
- **Small amounts only.** This app is designed for pocket-money-scale sats. Don't keep meaningful amounts in children's wallets

### What needs to be built for real payments

**None of the following exists yet.** The following is what a developer would need to build to make real Lightning payments work:

1. **Invoice generation** — call `POST /api/v1/invoices` on each child's LNbits wallet to generate a real bolt11 invoice and display it as a scannable QR code
2. **Payment** — call `POST /api/v1/payments` on the parent's LNbits wallet to pay the invoice
3. **Confirmation polling** — poll `GET /api/v1/payments/{payment_hash}` to confirm receipt before crediting the child's in-app balance
4. **Per-child wallet keys** — settings UI to store a separate invoice key per child rather than a single admin key

If you're a developer, these are all straightforward LNbits REST API calls. The settings infrastructure (URL, API key storage, test connection) is already built. Pull requests welcome.

---

## For developers

SatsKids is a single self-contained HTML file with no build step, no dependencies, and no framework. Everything is vanilla JS, CSS, and Web Audio API.

- **State** — stored in `localStorage` as `satskids_v2` (JSON)
- **Price data** — fetched from CoinGecko, Coinbase, and Blockchain.info in sequence (first success wins)
- **Sounds** — synthesised via Web Audio API, no external files
- **Fonts** — Nunito and Space Mono loaded from Google Fonts

To contribute: fork, edit `index.html`, test in a browser, submit a PR. No build process needed.

### Roadmap
- [ ] Real Lightning payment flow via LNbits
- [ ] Per-child LNbits wallet key management
- [ ] Payment confirmation polling
- [ ] Animated educational illustrations (currently text + simple SVG)
- [ ] Multiple currency support (currently GBP/USD price fetched, GBP displayed)
- [ ] Sound on/off toggle
- [ ] PWA manifest for home screen installation
- [ ] Optional passcode for kids' view (to prevent balance tampering)

---

## Licence

MIT — do whatever you like with it. Attribution appreciated but not required.

---

## A note on financial advice

SatsKids is an educational tool. Simulated sats are not real money. Real sats are volatile and you can lose value. The future projections shown (25% annual growth) are illustrative only and not a prediction or recommendation. Please don't put more into your children's wallets than you'd be comfortable losing.

Bitcoin is brilliant. So is teaching kids about it early. But please do your own research.

---

*Built with ⚡ as a Saturday morning project. Feedback and contributions welcome.*

[github.com/Bitcoin-Lebowski/satskids](https://github.com/Bitcoin-Lebowski/satskids)
