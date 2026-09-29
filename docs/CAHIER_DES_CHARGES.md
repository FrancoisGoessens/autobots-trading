# Cahier des charges — Autobots Trading

Version 0.1 — 29/09/2026. À valider par François : les valeurs marquées **(proposé)** sont des propositions, pas des décisions.

> Ce projet est une expérience d'apprentissage. Ce n'est pas un conseil financier, et rien ici ne garantit un gain.

## 1. Objectif

Construire, de A à Z, un système d'analyse et de trading crypto piloté par Claude, pour observer comment il raisonne, optimise et monte un projet en autonomie. Le succès se mesure d'abord à ce qu'on apprend (méthode, outillage, honnêteté des résultats), ensuite au rendement.

Le système doit produire de façon régulière :
- des tableaux d'analyse (prix, volatilité, corrélations, régimes de marché) ;
- des synthèses de tendances et d'actualité ciblée ;
- des hypothèses de trading testables, avec leurs résultats chiffrés ;
- à terme, des décisions de trading en simulation, puis en réel.

## 2. Périmètre de la V1

- **Marché** : crypto uniquement, spot uniquement, sans levier, sans futures. Bourse (Nasdaq, Wall Street) en V2 si la V1 aboutit.
- **Socle technique** : Freqtrade, avec FreqAI pour la partie apprentissage automatique. Python.
- **Paires** : liste blanche courte au départ, BTC/USDT et ETH/USDT **(proposé)**, élargie seulement après validation.
- **Exchange** : à choisir avec François (disponible en France, API avec permissions granulaires, frais bas, données historiques exploitables).
- **Exécution** : les processus permanents tournent sur l'ordinateur portable de François (ancien Asus TUF), pas sur un serveur payant.

## 3. Points de comparaison (benchmarks)

Toute stratégie est jugée nette de frais et comparée à :
1. **Buy and hold** sur la même période et les mêmes paires.
2. **Placer les versements sur un ETF monde** : repère de long terme d'environ 7 %/an historiquement, sans garantie.
3. **Une stratégie de référence très simple** (par exemple croisement de moyennes mobiles), construite en phase 1. Si un modèle ML ne la bat pas nettement, il n'a aucun intérêt.

## 4. Cible de rendement

La cible de François est de **5 %/mois**, avec 50 €/mois versés. C'est un **résultat à mesurer, pas un paramètre à régler** : les mises sont dimensionnées sur le risque (section 6), jamais pour « atteindre » un chiffre. Si le système fait moins, c'est un résultat valable et instructif. Le tableau d'intérêts composés sur 12 mois (600 € versés, environ 836 € à la fin) sert de repère d'ambition, pas d'engagement.

Rappel de calcul : un winrate de 51 % ne suffit pas. Ce qui compte est l'espérance par trade :
`espérance = winrate × gain moyen − (1 − winrate) × perte moyenne − frais`.

## 5. Phases et critères de passage

Chaque passage de phase est une porte (gate) avec des critères écrits **avant** de voir les résultats. Le critère de passage n'est jamais « je pense que ça marche ».

### Phase 0 — Fondations
Repo, structure, environnement reproductible, journal d'expériences.
Porte : un backtest de démonstration tourne de bout en bout sur l'Asus.

### Phase 1 — Données et backtest fiable
- Collecte et stockage des historiques (chandelles multi-timeframes) avec contrôle qualité (trous, doublons, anomalies).
- Backtest reproductible : frais réalistes (0,1 % par ordre **(proposé)**) et slippage estimé.
- Séparation stricte : entraînement / validation / **test final jamais touché** avant la décision finale. Validation par fenêtres glissantes (walk-forward).
- Stratégie de référence et benchmarks calculés.

Porte : résultats identiques à chaque relance, benchmarks calculés, jeu de test final scellé.

### Phase 2 — Recherche d'hypothèses et de signaux
- Chaque hypothèse (ex. « après telle configuration de bougies dans telle tendance, le prix monte ») est **écrite avant d'être testée** dans `experiments/`, avec sa règle exacte.
- Le nombre d'hypothèses testées est compté : plus on en teste, plus il faut de preuves (risque de trouver des motifs qui n'existent que par hasard).
- Modèles simples et régularisés en priorité ; complexité ajoutée seulement si elle bat le simple sur la validation.

Porte **(proposé)** : au moins un signal ou modèle qui, hors échantillon et net de frais, dépasse la stratégie de référence, avec un ratio de Sharpe > 1 et un profit factor > 1,3, sur au moins 100 trades.

### Phase 3 — Simulation en conditions réelles (dry-run)
- Freqtrade en dry-run sur données live, avec un capital fictif de 30–50 € par mois versé, comme prévu.
- Durée minimale **(proposé)** : 60 jours et 100 trades.
- Comparaison continue backtest / simulation : un écart important signale un défaut du backtest.

Porte **(proposé)** : drawdown maximal < 20 %, performance nette positive, Sharpe > 1 sur la période, écart backtest/simulation expliqué.
**François valide explicitement le passage** après lecture d'un rapport complet.

### Phase 4 — Réel, capital plafonné
- Portefeuille dédié, versements mensuels, plafond de capital total fixé par François (à définir).
- Tous les garde-fous de la section 6 actifs et testés avant le premier ordre.
- Revue mensuelle automatique. Toute violation de règle arrête le système.

## 6. Garde-fous

Ils sont **codés en dehors de l'agent et des modèles**, dans un module dédié, et je ne peux pas les modifier sans commit explicite de François.

- Clé API autorisée à **trader mais pas à retirer**, restreinte à l'IP de l'Asus si l'exchange le permet.
- Pas de levier, pas de marge, pas de futures.
- Paires en liste blanche uniquement.
- Risque maximal par trade : 1 % du capital **(proposé)**.
- Perte journalière maximale : 3 % **(proposé)** → pause jusqu'au lendemain.
- Drawdown maximal depuis le plus haut : 15 % **(proposé)** → arrêt complet et notification.
- Nombre maximal d'ordres par jour et taille maximale d'ordre.
- Kill switch : une commande unique qui coupe les nouveaux ordres et, si voulu, ferme les positions.
- Aucune clé, secret ou identifiant dans le repo ni dans les journaux.
- En cas de doute (donnée manquante, exchange en panne, comportement inattendu) : ne rien faire et alerter.

## 7. Budget de tokens

Règle centrale : **tout ce qui est déterministe tourne en code, sans token**.

- Sans token : collecte de données, indicateurs, backtests, simulation, exécution, monitoring, génération des tableaux et graphiques.
- Avec token, à basse fréquence : digest quotidien court (quelques milliers de tokens), revue hebdomadaire des expériences, interprétation d'anomalies, évolution de l'outillage.
- Les résultats sont écrits dans des fichiers compacts (résumés chiffrés) pour que je n'aie jamais à relire des données brutes.
- Un watchdog en script décide s'il faut me réveiller ; par défaut, non.
- Crédits cloud (100 $, expirent le 5 novembre 2026) : à utiliser pour la phase de construction (phases 0 et 1 surtout), qui est la plus consommatrice.

## 8. Architecture cible

```
collecteurs (cron/systemd)  ->  data/ (chandelles, news brutes)
                                   |
                     backtest / features / modèles (code)
                                   |
                       experiments/ (résultats chiffrés)
                                   |
   watchdog (script) --réveille--> agent Claude (digest, revue, décisions de recherche)
                                   |
                       reports/ (tableaux, tendances, articles)
                                   |
                  Freqtrade dry-run -> (après validation) réel
                                   |
                      garde-fous (module indépendant)
```

Agents prévus (chacun avec une mission courte et un format de sortie fixe) :
- **Analyste** : digest quotidien du marché à partir des fichiers de synthèse.
- **Chercheur** : formule et enregistre des hypothèses, lance les backtests, résume.
- **Relecteur** : contrôle indépendant des résultats (surapprentissage, fuite de données, frais oubliés) avant toute porte.

L'état du projet vit dans le repo (`STATE.md`, `experiments/`, `reports/`) pour qu'une nouvelle session reparte rapidement.

## 9. Risques connus

- **Surapprentissage** : traité par test final scellé, comptage des hypothèses, modèles simples d'abord.
- **Fuite de données** (le modèle « voit » le futur) : contrôle par le relecteur.
- **Chance en simulation** : durées et nombres de trades minimaux fixés à l'avance.
- **Marché** : les régimes changent ; une stratégie qui marchait peut cesser de marcher.
- **Technique** : panne de l'Asus, de l'exchange ou du réseau → le système doit échouer de façon sûre.
- **Fiscalité** : les gains crypto peuvent être imposables en France ; à vérifier par François, je ne fais pas de conseil fiscal.

## 10. Questions ouvertes pour François

1. Exchange préféré, ou je te propose un comparatif ?
2. Plafond de capital total en phase réelle ?
3. Valides-tu les seuils marqués (proposé) ?
4. L'Asus peut-il rester allumé en continu, et sous quel système (Windows ou Linux) ?
5. Souhaites-tu des notifications (email, Telegram, autre) en cas d'alerte ?
