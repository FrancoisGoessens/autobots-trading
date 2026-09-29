# Veille : réduire les tokens sans changer les résultats

Date : 29/09/2026. Méthode : recherche web, puis lecture des pages GitHub des candidats. Les chiffres d'économie viennent des README des projets eux-mêmes : aucun n'a été reproduit ici. Rien n'est installé.

## Ce qui est vérifié, et ce qui ne l'est pas

- Vérifié (lu sur les pages GitHub le 29/09/2026) : étoiles, forks, issues ouvertes, licence, dates des dernières versions pour rtk et headroom.
- Non vérifié : les pourcentages d'économie (auto-déclarés), l'absence de perte de qualité (sauf pour Headroom, qui publie des tests, non rejoués), l'ancienneté exacte des dépôts.
- « Les mieux notés des 3 derniers mois » n'est pas mesurable proprement : GitHub ne donne pas les étoiles gagnées sur 3 mois. J'ai donc retenu des dépôts au nombre d'étoiles élevé **et** avec des versions publiées dans les 3 derniers mois.
- Le format de dates de la page des versions de snip (2024) est incohérent avec son code (Go 1.25+), donc je ne m'en sers pas.

## Classement

| Rang | Dépôt | Étoiles | Activité récente (vérifiée) | Ce qu'il fait |
|---|---|---|---|---|
| 1 | rtk-ai/rtk | 80,4 k | versions du 28/07 au 13/08/2026 | Proxy en ligne de commande qui condense la sortie de commandes (tests, git, lint, build) avant qu'elle n'entre dans le contexte |
| 2 | chopratejas/headroom | 74,1 k | versions du 20/08 au 26/09/2026 | Compresse sorties d'outils, logs, JSON et fichiers ; bibliothèque, proxy ou serveur MCP |
| 3 | zilliztech/claude-context | 12,6 k | non vérifiée | Recherche sémantique de code par MCP |
| 4 | edouard-claude/snip | 427 | dates non fiables | Alternative à rtk en Go, filtres en YAML |
| 5 | sliday/tamp | 92 | non vérifiée | Proxy de compression, dont des étapes avec perte |

## Choix

**Premier choix : rtk.** Licence Apache 2.0, binaire unique, très actif, et il cible exactement ce que consomment mes sessions de développement : la sortie de `pytest`, `git`, `ls`, `ruff`. Ses économies annoncées vont de 60 à 90 % sur ces commandes. Réserves : la documentation reconnaît que cette réduction porte sur la sortie bash seulement et se dilue dans la facture totale ; 836 issues et 774 PR ouvertes indiquent un projet très sollicité, avec un risque de bugs de filtrage. Le vrai risque pour « sans changer les résultats » : un filtre qui masque une ligne d'erreur utile.

**Second choix : headroom**, à envisager pour le pipeline de digest quotidien, pas pour mes sessions de dev. Il annonce 60 à 95 % de tokens en moins sur du JSON, ce qui colle à des données de marché structurées. Il publie des mesures de qualité (GSM8K identique à 0,870, SQuAD v2 à 97 % pour 19 % de compression, sur 100 exemples). Limite : sur la prose et les contenus déjà denses, il gagne peu ou rien.

**Écartés :**
- claude-context : exige une clé API OpenAI et une base vectorielle Zilliz Cloud, ce qui contredit notre règle « pas d'API » et ajoute un coût. Notre repo est petit : l'indexation sémantique n'apporterait presque rien.
- snip : 427 étoiles, dates de versions illisibles ; rtk fait la même chose avec bien plus de traction.
- tamp : 92 étoiles, et ses niveaux d'économie élevés sont avec perte (compression neuronale), donc incompatibles avec « sans changer les résultats ».
- LLMLingua : bibliothèque de recherche à compression avec perte, pas un outil pour agents de code.

## Ce que ça pèse vraiment pour Autobots Trading

Le levier principal est déjà l'architecture : collecte, indicateurs, backtests, simulation et monitoring tournent en scripts, sans aucun token. Ces outils ne grappillent que la partie « sessions de développement » et le digest quotidien. Ne pas s'attendre à 90 % de la facture totale.

## Proposition de test avant adoption (à faire en phase 0, avec ton accord)

1. Choisir une tâche de développement moyenne et répétable (par exemple installer Freqtrade et lancer un backtest de démo).
2. La jouer deux fois : sans rtk, puis avec rtk, dans les mêmes conditions.
3. Mesurer par script les tokens consommés et vérifier que le résultat final est identique (mêmes tests verts, même sortie de backtest).
4. Adopter seulement si les tokens baissent nettement et que rien n'est masqué.

Même démarche plus tard pour headroom sur le digest : mesurer d'abord la taille réelle des entrées avant de décider.

## Sources

- [rtk-ai/rtk](https://github.com/rtk-ai/rtk) et [ses versions](https://github.com/rtk-ai/rtk/releases)
- [chopratejas/headroom](https://github.com/chopratejas/headroom) et [ses versions](https://github.com/chopratejas/headroom/releases)
- [zilliztech/claude-context](https://github.com/zilliztech/claude-context)
- [edouard-claude/snip](https://github.com/edouard-claude/snip)
- [sliday/tamp](https://github.com/sliday/tamp)
- [microsoft/LLMLingua](https://github.com/microsoft/LLMLingua)
- [pleasedodisturb/awesome-llm-token-optimization](https://github.com/pleasedodisturb/awesome-llm-token-optimization) (liste de départ pour d'autres candidats)
