# Modele manuel ou modele AutoML ? (TP05)

Mesures locales : Python 3.12, FLAML 2.7.0, MLflow 3.17.2, jeu v1 (5000 transactions),
meme decoupage pour les deux modeles (25 % de test, `random_state=42`).
Latence : 200 appels mesures apres 10 d'echauffement, services `mlflow models serve` en local,
chaque service mesure deux fois.

| Critere                    | Manuel (rf 500/16)   | AutoML (FLAML 90 s)   |
|----------------------------|----------------------|-----------------------|
| Famille de modele          | foret aleatoire      | XGBoost (XGBClassifier, 333 arbres, 4 feuilles max) |
| F1 sur le jeu de test      | 0.8669               | 0.8582                |
| Temps de recherche machine | 1.8 s (un seul fit, reglage trouve au TP2) | 90.4 s (recherche) |
| Temps humain (estimation)  | eleve : plusieurs essais compares a la main au TP2 (estimation : 1 a 2 h) | faible : une commande (estimation : 10 min) |
| Latence mediane (ms)       | 59.0 et 59.0         | 12.8 et 12.7          |
| Latence p95 (ms)           | 65.9 et 63.5         | 14.3 et 14.1          |
| Taille du fichier (Mo)     | 13.37                | 0.29                  |
| Explicabilite              | importance des variables, 500 arbres de profondeur 16 (difficile a lire arbre par arbre) | importance des variables, 333 arbres de 4 feuilles max (plus petit), journal des essais FLAML |

## Decision (5 lignes) : quel modele mettre en production, et pourquoi ?

1. Je mettrais le **modele AutoML (XGBoost)** en production : il est environ 4,6 fois plus rapide (12,8 ms contre 59 ms en mediane) et environ 46 fois plus leger (0,29 Mo contre 13,37 Mo).
2. Son F1 est un peu plus bas (0.8582 contre 0.8669, soit 0.0087 d'ecart), mesure sur un seul decoupage de test : l'ecart est faible et peut tenir au hasard du decoupage.
3. Si la priorite etait le meilleur F1 a tout prix, le modele manuel resterait un choix defendable, il passe le seuil de 0.82 comme l'autre.
4. Le modele AutoML est plus petit, donc plus simple a mettre dans une image Docker ou a embarquer, et son journal de recherche (`reports/flaml_tp5.log`) documente ce qui a ete essaye.
5. Dans les deux cas l'alias `production` permet de basculer ou de revenir en arriere sans changer le code du client.

## Limites

- Un seul jeu de donnees et un seul decoupage de test ; les latences dependent de la machine.
- Le temps humain est une estimation, non une mesure.
- Parcours cloud (bloc G, facultatif) non realise : aucun compte cloud utilise.
