# Autobots Trading

<p align="center"><img src="assets/logo.svg" alt="Logo provisoire Autobots Trading" width="120"></p>

Expérience de trading crypto piloté par Claude : construire de A à Z des agents, des outils et des scripts capables de produire des analyses de marché, de s'entraîner en simulation, puis, sous garde-fous stricts, d'opérer avec un petit portefeuille réel.

Le but est d'apprendre, pas de battre le marché. Ce n'est pas un conseil financier.

## Documents
- [Cahier des charges](docs/CAHIER_DES_CHARGES.md) : objectifs, critères de passage, garde-fous, budget de tokens, architecture
- [Roadmap](docs/ROADMAP.md)
- [État du projet](STATE.md)

## Structure
```
assets/        logo provisoire
docs/          cahier des charges, roadmap
src/
  collectors/  collecte de données (sans tokens)
  backtest/    backtests reproductibles
  sim/         simulation (dry-run)
  guardrails/  garde-fous, hors de portée de l'agent
agents/        définitions des agents (analyste, chercheur, relecteur)
experiments/   hypothèses et résultats chiffrés
reports/       tableaux, tendances, articles générés
data/          données locales (non versionnées)
```

## Statut
Phase 0 — Fondations. Voir [STATE.md](STATE.md).
