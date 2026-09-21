# LiqHunter Direction Analyzer

[![Validate LiqHunter](https://github.com/ahmetbysoy/liqhunter-direction-analyzer/actions/workflows/validate.yml/badge.svg)](https://github.com/ahmetbysoy/liqhunter-direction-analyzer/actions/workflows/validate.yml)

LiqHunter is a Boolean-first cryptocurrency market direction analysis PWA. It explains direction candidates with evidence and uncertainty. It does not place orders, manage positions, or provide trading execution.

## Current status

- Stage: prototype baseline
- Data: browser-side Binance Futures public REST and WebSocket reads with a demo fallback when live reads are unavailable
- UI: Discover, Market, Forecasts, and Bot screens
- CI: required files, manifest JSON, analysis-only boundary, and README freshness are checked on pushes and pull requests
- Next phase: Node.js data bridge, SQLite prediction history, modular Boolean-first state, and automated tests

The production architecture is not complete yet. The current browser prototype is intentionally kept separate from the planned backend data bridge.

## Current contents

- `liqhunter-pwa.html`: mobile-first PWA prototype with live-read attempt and analysis-only UI.
- `manifest.webmanifest`, `sw.js`, `icon.svg`: installable PWA shell.
- `liqhunter-plan.html`: product and implementation plan.
- `boolean-first-audit.md`: audit of the source snapshot and the approved state model.
- `.github/workflows/validate.yml`: CI checks for the project and this README.

## Run locally

Serve the repository with any static web server, then open `liqhunter-pwa.html`. Opening the file directly may prevent service worker registration.

## Scope boundary

This project is analysis-only:

- No order placement
- No positions, TP/SL, leverage, or trading execution
- No investment guarantee
- Real data must be available before a production analysis is shown

## Data note

The prototype attempts Binance Futures public REST and WebSocket reads directly in the browser. CORS, network, or provider availability can prevent those reads. The final design moves this work to a Node.js service and stores prediction history in SQLite.

## README maintenance

This README is part of the project contract. Update it in the same commit whenever a public feature, core file, workflow, data source, scope boundary, or implementation stage changes. CI fails when a core project file changes without a corresponding README update.

## Next implementation phase

The approved v2 rewrite is planned in small, testable steps:

1. Boolean-first state contract and named constants.
2. Node.js Binance REST/WebSocket adapters with reconnect and data freshness states.
3. Indicator, direction, uncertainty, and prediction-history modules.
4. SQLite persistence and backtestable prediction events.
5. PWA render/event modules, offline/update behavior, and tests.
