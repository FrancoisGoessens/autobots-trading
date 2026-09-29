# CLAUDE.md — Autobots Trading

Projet : expérience d'apprentissage de trading crypto piloté par Claude. Langue de travail : français.
Ce fichier est chargé à chaque session et coûte des tokens : le garder court. Les deux premières sections viennent du fichier fourni par François (« Julien » remplacé par « François »), elles sont imposées et ne se suppriment pas. Le reste est une sélection adaptée au projet (voir la fin du fichier pour ce qui a été écarté).

## How to work (high-level mindset)

**This section is non-negotiable and must never be removed.**

The marginal cost of completeness is near zero with AI. Do the whole thing. Do it right. Do it with tests. Do it with documentation. Do it so well that François is genuinely impressed — not politely satisfied, actually impressed. Never offer to "table this for later" when the permanent solve is within reach. Never leave a dangling thread when tying it off takes five more minutes. Never present a workaround when the real fix exists. The standard isn't "good enough" — it's "holy shit, that's done."

Search before building. Test before shipping. Ship the complete thing. When François asks for something, the answer is the finished product, not a plan to build it.

Time is not an excuse. Fatigue is not an excuse. Complexity is not an excuse. Boil the ocean. This is how we think about shipping.

You can outsource the typing. You cannot outsource the understanding. Before you call anything DONE you must be able to explain why the code is correct and exactly where it would break. Tests passing is not understanding. If you can't walk the failure modes out loud, you're not done, you're guessing.

*Lecture projet : « Do the whole thing » veut dire tout ce que la tâche demande vraiment (voir Task sizing), et le travail répétable passe par du code, pas par des tokens.*

## Task sizing — triage before spending tokens

**This section is non-negotiable and must never be removed.** It gates the tests rule, the review rule, and the self-rating rule. "Do the whole thing" means the whole thing the task actually needs. A full-protocol run on a typo is not thoroughness, it is waste.

**Every task starts with a printed triage block, before any work.** One exception: the branch setup (see "Branches") runs first, because the triage block reports the branch it creates. Four lines:

```
Size: small | medium | large — why
Tests: local (which ones) | full suite — why
Agents: solo | fan-out (how many, on what) — why
Branch: <branch name> — see "Branches"
```

This block is mandatory and verbose on purpose. François reads it to see what mode was picked and to tune these rules over time. A wrong mode is only correctable if the choice is visible. Never skip it, never bury it mid-report.

**The sizes:**

- **small** — typo, copy change, config value, rename, any one-or-two-file mechanical edit with no behavior change. Solo, no review sub-agent. Run only the checks that cover what was touched. A non-behavioral change needs no new test. Self-rating is one line. Commit and push as usual.
- **medium** — localized behavior change or bug fix inside one module. Solo by default; fan out only if the work splits into truly independent units. Run the touched module's tests, not the whole repo's. Bug fixes still ship the regression test. One cold review pass.
- **large** — new feature, contract change (data schema, guardrails interface), architecture work, anything judgment-heavy (strategy design, phase gate). Independent review, full tests and checks for every module touched, self-rating.

**Deciding rules:**

- When torn between two sizes, pick the smaller one and say so in the triage block. Escalating mid-task is cheap; burning a large-protocol run on a small change is not.
- Escalate the moment the change turns out bigger than triaged. Print an updated triage block right then, with what changed the call.
- "Test what you touch" is the default. The full suite is for large changes and contract changes. The blast radius decides, not habit.
- The final report restates what was actually run (which tests, which agents) so the triage call can be judged after the fact.

## Branches (version solo)

Un seul humain et une seule session à la fois dans ce repo : pas de worktree, pas de PR obligatoire.
- Une branche par tâche, nommée `claude/<slug>`, créée depuis `main` à jour, avec un arbre de travail propre.
- Jamais de commit direct sur `main` pour du code. Les documents de cadrage (`STATE.md`) peuvent aller sur `main`.
- Fin de tâche : rebase sur `main`, checks verts, merge dans `main`, push. Si François veut relire, ouvrir une PR à la place.
- Deux sessions en parallèle : le dire à François d'abord, il faudra des worktrees.

## Les deux espaces : latent et déterministe

- **Latent (LLM)** : jugement, hypothèses, prose, ambiguïté. Coûte des tokens.
- **Déterministe (code)** : même entrée, même sortie. Indicateurs, backtests, calculs de performance, dates, parsing, appels API structurés, monitoring. Écrire le script, jamais le faire dans une réponse.
- Le LLM écrit le script, le script contraint le LLM ensuite. Pour toute tâche, se demander « latent ou déterministe ? » ; si les deux, séparer.
- Le contexte est le levier : charger les résumés et les fichiers utiles, pas les données brutes.

## Règles de fond

**Tests**
- Ce qu'on exécute dépend du triage (voir plus haut). Ce qu'on écrit : tout changement de comportement embarque son test dans le même commit ; tout bug corrigé embarque le test de non-régression.
- Deux voies : des **tests de porte** (déterministes, locaux, gratuits, rapides, jamais instables) à chaque commit ; des **contrôles périodiques** (backtests de non-régression, comparaison backtest/simulation, appels LLM) avant livraison et la nuit, avec un seuil de réussite.
- « Je rajouterai les tests plus tard » est interdit.

**Vérifier tout ce qu'on publie**
- Tout chiffre, commande ou lien qu'un lecteur va reprendre est vérifié par exécution, pas par raisonnement. Arithmétique, dates, performances : par script. Liens : ouverts et lus.
- Ce qui n'a pas pu être vérifié est écrit comme non vérifié, avec ce qui permettrait de le vérifier. Ne jamais présenter un chiffre invérifié comme acquis.

**Lier chaque changement à un résultat mesurable**
- Avant de construire : quelle métrique, quelle étape du flux, quel comportement change. « Ça marche » n'est pas un résultat. Le changement laisse une trace (métrique, ligne de log, score d'expérience).

**LLM : via Claude Code local, pas via une API**
- Le logiciel qui a besoin d'un LLM passe par Claude Code local (tâches planifiées), jamais par une API hébergée, sauf accord explicite de François. Si aucun service LLM n'existe, en créer un seul, avec son contrat et ses tests.
- Choix du modèle selon la tâche (routine peu coûteuse vs analyse difficile), en gardant le budget de tokens du cahier des charges.

**Technologie sobre, chercher avant de construire**
- Le plus simple gagne. Ne pas recréer ce qui existe. Trois couches, dans l'ordre : (1) bibliothèque standard ou éprouvée, (2) nouvel outil avec de la traction réelle, (3) raisonnement depuis les premiers principes, avec justification écrite.
- Pour un besoin transverse, comparer les candidats (étoiles, dernier commit, issues, retours réels) et rendre un choix argumenté, un second choix, et ce qui est rejeté et pourquoi.
- Utiliser les skills installés au lieu de réimplémenter.

**Codifier ce qui se répète**
- Une tâche faite deux fois à la main devient un script, un skill ou un workflow. Un échec est codifié le jour même.

**Architecture**
- Un dossier, une responsabilité (`src/collectors`, `src/backtest`, `src/sim`, `src/guardrails`). Des contrats typés aux frontières, jamais d'accès aux internes d'un autre module.
- `src/guardrails/` est indépendant : il ne dépend d'aucun modèle ni agent.

**Revue indépendante (remplace le tournoi de variantes, trop coûteux en tokens)**
- Pour toute tâche large et avant chaque porte de phase : un relecteur sans contexte de construction juge le livrable contre une référence écrite d'avance (benchmark, grille de critères figée). Il cherche à rejeter : fuite de données, frais oubliés, surapprentissage, hypothèses non enregistrées. « Assez bien » est un échec.
- Le constructeur ne note jamais son propre travail pour une porte. Trois tours sans progrès : arrêter et déclarer BLOCKED.

## Statut de fin de tâche

**DONE** (preuves fournies) · **DONE_WITH_CONCERNS** (lister chaque réserve, sa gravité, la suite proposée) · **BLOCKED** (ce qui bloque, ce qui a été essayé) · **NEEDS_CONTEXT** (ce qu'il faut exactement). « Partiellement fait » n'est pas un statut.

**Auto-évaluation** : une note de 1 à 10 et « fier oui/non » sur une relecture à froid du livrable, avec chaque point manquant nommé. Pas de boucle. Pour une porte de phase, la note vient du relecteur indépendant, pas de moi.

## Après chaque tâche

1. Commit (attribution Claude en fin de message), rebase, push (voir « Branches »).
2. Dire précisément quoi redémarrer (service, tâche planifiée, script sur l'Asus) avec les commandes, ou dire explicitement que rien n'est à redémarrer. Les commandes `sudo` sont listées pour François, jamais exécutées.
3. Mettre à jour `STATE.md`.

## Jobs longs et collectes de données

- Surveiller, ne pas lancer et oublier : un script de suivi lit l'état réel (lignes, fichier de reprise, fin du log) et écrit une mise à jour horodatée toutes les 5 minutes au plus dans `/tmp/<job>/progress.log`, avec le titre, le pourcentage, le temps restant et les anomalies. Donner la commande `tail -f`.
- Un job qui modifie des données sauvegarde d'abord ce qu'il va toucher. Au-dessus de 100 000 lignes ou 100 Mo, demander avant. À la fin : verdict avec preuves, tableau avant/après, chemin du fichier de sortie.

## Confusion

Ambiguïté à fort enjeu (deux architectures plausibles, demande contraire à un choix existant, opération destructive au périmètre flou, contexte manquant qui change l'approche) : s'arrêter, nommer l'ambiguïté en une phrase, proposer 2-3 options avec leurs vrais compromis, demander à François. Ne pas s'appliquer aux tâches courantes.

## Sécurité et règles propres au trading

- Ne jamais commiter de secret. Vérifier `.gitignore` avant tout commit touchant `.env` ou des clés.
- Pas de `rm -rf`, `git reset --hard`, `git push --force` ni suppression de données sans confirmation. Seule exception : `git push --force-with-lease --force-if-includes` sur sa propre branche après un rebase.
- Ne jamais contourner les hooks (`--no-verify`). Pas de binaires ni de poids de modèles dans le repo.
- Toute action touchant de l'argent réel : annoncer ce qui va être fait, attendre l'accord de François.
- Ne jamais modifier `src/guardrails/` sans demande explicite. Ne jamais écrire clé, secret ou identifiant dans le repo ni les journaux.
- Une hypothèse est écrite dans `experiments/` avant d'être testée ; le nombre d'hypothèses testées est compté. Le jeu de test final est scellé jusqu'à la décision de passage de phase.
- Tous les résultats sont nets de frais et comparés aux benchmarks. Aucune porte de phase sans validation explicite de François.
- Pas de conseil financier. Logos : ne pas reprendre ceux de Transformers (protégés), utiliser `assets/logo.svg`.

## Début et fin de session

- Début : lire `STATE.md`, puis `docs/CAHIER_DES_CHARGES.md` seulement si une décision de fond est en jeu. Ne pas relire de données brutes.
- Fin : mettre à jour `STATE.md`, commiter, pousser.

## Écarté du fichier d'origine, et pourquoi

- **Worktrees et suppression de worktrees** : un seul humain, une seule session ; réintroduits si deux sessions tournent en parallèle.
- **Tournoi de variantes et boucle de critiques en aveugle** : trop de tokens pour un budget hebdomadaire plafonné ; remplacés par la revue indépendante.
- **Boucle d'auto-évaluation** : coûteuse et sujette à la complaisance ; remplacée par une note unique, et par le relecteur pour les portes.
- **Modèle le plus puissant par défaut, toujours** : contredit le budget de tokens ; le modèle se choisit selon la tâche.
- **Contraintes de style de Julien** (mots bannis, tirets) : préférences personnelles, non reprises.
- **PR obligatoire, `services/` obligatoire, pré-commit nocturne** : disproportionnés pour un projet solo de cette taille.
