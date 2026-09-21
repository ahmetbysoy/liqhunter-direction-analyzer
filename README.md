# LiqHunter Direction Analyzer

LiqHunter is a Boolean-first cryptocurrency market direction analysis PWA. It explains direction candidates with evidence and uncertainty. It does not place orders, manage positions, or provide trading execution.

## Current contents

- `liqhunter-pwa.html`: mobile-first PWA prototype with Discover, Market, Forecasts, and Bot screens.
- `manifest.webmanifest`, `sw.js`, `icon.svg`: installable PWA shell.
- `liqhunter-plan.html`: product and implementation plan.
- `boolean-first-audit.md`: audit of the source snapshot and the approved state model.

The prototype attempts Binance Futures public REST and WebSocket reads in the browser. If those reads are unavailable, it shows its current demo state. The planned production architecture moves the data bridge to Node.js and uses SQLite for durable prediction history.

## Run locally

Serve the repository with any static web server, then open `liqhunter-pwa.html`. Opening the file directly may prevent service worker registration.

## Scope boundary

This project is analysis-only:

- No order placement
- No positions, TP/SL, leverage, or trading execution
- No investment guarantee
- Real data must be available before a production analysis is shown

## CI

GitHub Actions checks required files, manifest JSON, and the analysis-only execution boundary on pushes and pull requests.

## Next implementation phase

The next approved phase is the modular v2 rewrite: Node.js Binance REST/WebSocket data bridge, SQLite persistence, Boolean-first state store, indicator and prediction modules, reconnect/error states, and tests.