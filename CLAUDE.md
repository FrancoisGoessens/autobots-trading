# Consignes pour les sessions Claude

Projet : Autobots Trading, expérience d'apprentissage de trading crypto piloté par Claude. Langue de travail : français.

## Au début de chaque session
1. Lire `STATE.md`, puis `docs/CAHIER_DES_CHARGES.md` si une décision de fond est en jeu.
2. Ne pas relire de données brutes : utiliser les résumés dans `reports/` et `experiments/`.

## À la fin de chaque session
- Mettre à jour `STATE.md` (fait, en cours, prochaines étapes, décisions).
- Commiter et pousser.

## Règles de fond
- Tout ce qui est déterministe tourne en code, sans tokens. Ne pas faire « à la main » ce qu'un script peut faire.
- Une hypothèse est écrite dans `experiments/` avant d'être testée. Le nombre d'hypothèses testées est compté.
- Le jeu de test final est scellé : ne pas l'utiliser avant la décision de passage de phase.
- Tous les résultats sont nets de frais et comparés aux benchmarks.
- Ne jamais modifier `src/guardrails/` sans demande explicite de François.
- Ne jamais écrire de clé API, secret ou identifiant dans le repo ni dans les journaux.
- Ne passer aucune porte de phase sans validation explicite de François.
- Pas de conseil financier : ce projet est une expérience.
- Logos : ne pas reprendre ceux de Transformers (protégés). Utiliser `assets/logo.svg`.
