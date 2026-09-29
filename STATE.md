# État du projet

Ce fichier est mis à jour à la fin de chaque session pour qu'une nouvelle session reparte sans relire tout l'historique.

**Dernière mise à jour** : 29/09/2026

## Phase actuelle
Phase 0 — Fondations.

## Fait
- Repo créé et lié à Claude (lecture et écriture vérifiées).
- Cahier des charges v0.1 et roadmap rédigés.
- Structure des dossiers posée.
- CLAUDE.md réécrit : sections « How to work » et « Task sizing » imposées par François, le reste sélectionné et adapté (voir la fin du fichier pour ce qui est écarté).
- Veille sur les outils d'économie de tokens : `docs/VEILLE_TOKENS.md` (premier choix rtk, second headroom, rien d'installé).

## En cours
- Attente de la validation par François du cahier des charges (seuils marqués « proposé » et questions ouvertes, section 10).
- Attente de son accord pour le test A/B de rtk en phase 0.

## Prochaines étapes
1. Choix de l'exchange et de l'environnement d'exécution (Asus : Windows ou Linux).
2. Environnement Python reproductible et installation de Freqtrade.
3. Collecteur de données historiques et contrôle qualité.
4. Premier backtest de démonstration et stratégie de référence.

## Décisions prises
- V1 : crypto uniquement, spot, sans levier ; Freqtrade + FreqAI.
- Le 5 %/mois est un résultat à mesurer, pas un paramètre.
- Garde-fous codés hors de l'agent ; passage en réel sur critères chiffrés fixés à l'avance.
- Chaque tâche commence par un bloc de triage (taille, tests, agents, branche) ; une branche `claude/<slug>` par tâche.
- LLM via Claude Code local, pas d'API hébergée, sauf accord de François.

## Budget
- Crédits cloud : 100 $, expirent le 5 novembre 2026.
- Tokens hebdomadaires : 80 % du plan Pro maximum.
