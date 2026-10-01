# Projet 1 — Pipeline API : Consignes de clôture : 05 octobre 2026 à 23h59

## Contexte et objectif

Ce projet t'amène à construire un vrai pipeline de données, du point d'entrée d'une API publique jusqu'à un jeu de données propre et documenté. L'objectif n'est pas seulement d'obtenir un fichier final qui a l'air correct : c'est de savoir *pourquoi* chaque décision que tu as prise était la bonne, et de pouvoir le prouver.

Le jeu de données : **NYC TLC Yellow Taxi Trip Data**, accessible via l'API Socrata de la ville de New York. Tu choisiras un mois précis à extraire, transformer et valider.

- **Page du dataset** : [NYC TLC Trip Record Data — Yellow Taxi (2023)](https://data.cityofnewyork.us/Transportation/2023-Yellow-Taxi-Trip-Data/4b4i-vvec)
- **Endpoint API (Socrata SODA)** : `https://data.cityofnewyork.us/resource/4b4i-vvec.json`
- **Documentation officielle des champs TLC** : [TLC Trip Record Data Dictionary (PDF)](https://www.nyc.gov/assets/tlc/downloads/pdf/data_dictionary_trip_records_yellow.pdf)

---

## 1. Prérequis

Avant de commencer, assure-toi d'avoir en main :

- **Un compte NYC Open Data** sur [data.cityofnewyork.us](https://data.cityofnewyork.us) — nécessaire pour générer un jeton d'accès.
- **Un jeton d'application (App Token)** Socrata. Il se génère depuis ton profil développeur sur la plateforme, dans la section dédiée aux clés API. Ce jeton n'est pas un mot de passe : il identifie tes requêtes pour éviter les limitations de quota, mais il doit tout de même rester privé.
- **Un environnement Python fonctionnel** (3.10+ recommandé), avec au minimum : `requests`, `pandas`, `python-dotenv`, `matplotlib` et `jupyter` si tu comptes explorer les données dans un notebook.
- **Des bases sur les API REST et le format JSON** : comprendre ce qu'est la pagination, un paramètre de requête, un code de statut HTTP.
- **Des bases Git/GitHub** : tu vas devoir versionner ton travail proprement, en excluant ce qui ne doit jamais être public.

### Sources et documentation

Les liens ci-dessous couvrent tout ce dont tu as besoin pour ce projet. Garde-les sous la main plutôt que de chercher au hasard en cours de route :

- **API Socrata (SODA)** — doc officielle des paramètres de requête (`$limit`, `$offset`, `$order`, `$select`, `$where`) : [support.socrata.com — paginer au-delà de 1000 lignes](https://support.socrata.com/hc/en-us/articles/202949268)
- **requests** — doc officielle : [requests.readthedocs.io](https://requests.readthedocs.io)
- **pandas** — doc officielle : [pandas.pydata.org/docs](https://pandas.pydata.org/docs/) *(ressources complémentaires à venir — deux fichiers seront ajoutés directement dans le dépôt du projet)*
- **python-dotenv** — gestion des variables d'environnement : [pypi.org/project/python-dotenv](https://pypi.org/project/python-dotenv/)
- **logging** (module standard Python) — doc officielle : [docs.python.org/3/library/logging.html](https://docs.python.org/3/library/logging.html), et le guide pratique officiel [Logging HOWTO](https://docs.python.org/3/howto/logging.html) si tu découvres le module pour la première fois
- **argparse** (module standard Python) — pour gérer tes paramètres `--year`/`--month` en ligne de commande : [docs.python.org/3/library/argparse.html](https://docs.python.org/3/library/argparse.html)

Si l'un de ces points n'est pas clair pour toi, c'est le moment de combler la lacune — pas après avoir codé la moitié du pipeline.

---

## 2. Consigne

Construis un pipeline qui, à partir d'un mois donné (année + mois en paramètre), réalise automatiquement les étapes suivantes, dans l'ordre, sans intervention manuelle entre chaque étape :

1. **Extraction** de l'intégralité des trajets du mois demandé depuis l'API Socrata.
2. **Transformation** : nettoyage, typage correct, gestion des valeurs manquantes et des anomalies, puis **sauvegarde du résultat nettoyé au format CSV ou JSON**.
3. **Validation** : preuve chiffrée que le résultat est fiable et complet.

Un pipeline qui nécessite que tu lances trois scripts à la main dans le bon ordre n'est pas un pipeline — c'est une suite de scripts. Le mot "automatique" n'est pas un détail.

---

## 3. Directives

### Accès à l'API

- Stocke ton App Token dans un fichier `.env`, jamais en dur dans ton code.
- Ce fichier ne doit **jamais** être versionné sur Git.

### Pièges à anticiper

Le jeu de données a l'air simple en apparence. Il ne l'est pas complètement. Avant d'écrire la moindre règle de nettoyage, pose-toi ces questions et vérifie les réponses toi-même plutôt que de les supposer :

- Quel est le type Python réel de chaque colonne une fois les données chargées dans ton DataFrame ? Est-ce que ça correspond à ce que tu attendais en regardant le JSON brut ?
- Une valeur qui ressemble à un nombre dans la réponse de l'API est-elle vraiment stockée comme un nombre une fois en mémoire ?
- Certaines colonnes ont-elles des valeurs manquantes ? Sont-elles réparties au hasard, ou est-ce qu'elles suivent un schéma que tu peux identifier en croisant avec d'autres colonnes ?
- Est-ce que tous les montants financiers se recoupent logiquement entre eux ? Si un total ne correspond pas exactement à la somme de ses composantes, est-ce une erreur à corriger, ou un signal à documenter ?
- Tes seuils de nettoyage (distances, durées, montants) sont-ils justifiés par une vraie analyse de la distribution des données, ou choisis au hasard parce qu'ils "semblent raisonnables" ?

Si tu ne prends pas le temps d'une vraie exploration avant de coder ton nettoyage, tu vas probablement supprimer des lignes légitimes ou, pire, laisser passer une anomalie silencieuse. Un notebook d'exploration séparé de ton code de production est une bonne pratique, pas une perte de temps.

### Robustesse du pipeline

- Gère les erreurs réseau et les limitations de débit avec des tentatives de nouvelle requête (retry), pas un simple échec silencieux.
- Assure-toi que ta pagination est stable : deux exécutions du même mois doivent produire le même nombre de lignes, sans doublons ni trous.
- Écris tes fichiers de manière atomique (jamais de fichier à moitié écrit en cas d'interruption).
- Journalise (logs) ce que fait ton pipeline, étape par étape, avec des messages qui permettent de comprendre un échec sans relire tout le code.
- Une étape qui échoue doit arrêter le pipeline, pas laisser les étapes suivantes tourner sur des données incomplètes.

### Liberté d'architecture

Il n'y a **aucune arborescence imposée**. Que tu mettes ton code dans un dossier `src/`, `app/`, ou directement à la racine, que tes données brutes vivent dans `data/raw/` ou `raw_data/`, c'est ton choix. Ce qui compte :

- La structure doit être **cohérente** : un même type de fichier ne se retrouve pas éparpillé à trois endroits différents sans raison.
- Elle doit être **documentée** : n'importe qui (y compris toi dans six mois) doit comprendre où trouver quoi en lisant ton `README.md`, sans avoir à explorer tous les dossiers.
- Elle doit rester **lisible à l'échelle du projet** : une bonne architecture est celle qui te permettrait d'ajouter une nouvelle étape au pipeline sans tout réorganiser.

---

## 4. Livrable

### Ce que tu dois rendre

- **Le code source complet** du pipeline, dans un dépôt Git propre.
- **Un `README.md`** qui explique : ce que fait le projet, comment le lancer, où trouver quoi dans ton arborescence, et les choix importants que tu as faits (notamment tes seuils de nettoyage et pourquoi).
- **Le jeu de données final nettoyé, au format CSV ou JSON** (ou les instructions claires pour le régénérer si le fichier est trop volumineux pour Git).
- **Un rapport de qualité** (au format de ton choix — JSON, Markdown, peu importe) qui documente : combien de lignes ont été retirées, pour quelles raisons, et ce que tu as choisi de ne *pas* supprimer malgré une anomalie détectée, avec ta justification.

### Barème de validation — /100 points

Ton projet sera noté selon le barème ci-dessous. Chaque ligne est vérifiée soit automatiquement (script), soit par une lecture humaine ou assistée (pour juger la qualité d'une justification, pas juste sa présence).

**Règle éliminatoire — prioritaire sur tout le reste :** si un token, un mot de passe ou le fichier `.env` apparaît à un seul endroit de l'historique Git (même dans un commit ancien, même supprimé depuis), **le score est automatiquement 0/100**, quel que soit le reste du projet. Un secret qui fuite une fois reste compromis pour toujours, même si tu le supprimes après coup.

**Seuil de réussite : 70/100.** En dessous, le projet est "à revoir" ; à partir de 70, il est "validé".

#### Bloc 1 — Extraction (10 pts) · *vérification automatique*

| Critère | Points |
|---|---|
| Le total de lignes extraites correspond exactement au `count(*)` officiel de l'API pour le mois choisi | 5 |
| Aucun doublon dans les fichiers bruts | 3 |
| Gestion visible des erreurs réseau dans le code (retry, timeout) | 2 |

#### Bloc 2 — Transformation et qualité (30 pts) · *mixte*

| Critère | Points | Vérification |
|---|---|---|
| Rapport de qualité présent et structuré (lignes retirées, raisons, seuils utilisés) | 10 | Auto |
| Chaque seuil de nettoyage est justifié par une observation chiffrée (percentile, distribution) plutôt qu'une valeur arbitraire | 15 | Humaine / assistée |
| Au moins une anomalie "piège" identifiée et traitée avec jugement (documentée plutôt que supprimée par réflexe) | 5 | Humaine / assistée |

#### Bloc 3 — Robustesse et reproductibilité (20 pts) · *vérification automatique*

| Critère | Points |
|---|---|
| Le pipeline peut être relancé sur un autre mois sans modifier le code | 8 |
| Une étape qui échoue arrête bien le pipeline (pas de poursuite sur données incomplètes) | 6 |
| Pas de valeurs codées en dur qui devraient être des paramètres (seuils, chemins, dates) | 6 |

#### Bloc 4 — Documentation et présentation (40 pts) · *mixte*

| Critère | Points | Vérification |
|---|---|---|
| `README.md` complet et structuré (objectif, installation, lancement, arborescence expliquée) | 10 | Auto (présence des sections) |
| Clarté rédactionnelle du README — compréhensible par quelqu'un qui découvre le projet | 10 | Humaine / assistée |
| Les choix d'architecture technique (pagination, gestion des erreurs, seuils, format de sauvegarde) sont expliqués et argumentés dans le README, pas seulement mentionnés | 15 | Humaine / assistée |
| Dépôt propre (pas de données volumineuses versionnées par erreur, pas de fichiers temporaires) | 5 | Auto |


**Exemple concret pour le critère "choix d'architecture"** : lister n'est pas argumenter.
- ❌ *Listé* : "J'ai mis un timeout de 30 secondes sur les requêtes API."
- ✅ *Argumenté* : "J'ai mis un timeout de 30 secondes parce que l'API Socrata peut mettre du temps à répondre sur les grosses pages, et un timeout plus court causait des échecs inutiles pendant mes tests."

Même chose pour tes seuils de nettoyage, ton format de sauvegarde, ta stratégie de reprise sur interruption — le README doit dire *pourquoi*, pas juste *quoi*.

---

## 5. Soumission

**📅 Date limite : lundi 5 octobre 2026, 23h59 (heure de Dakar).** 

Une fois ton projet terminé et poussé sur GitHub (dépôt public, sinon je ne peux pas y accéder) :

- **Commente le post LinkedIn du projet** avec le lien direct vers ton dépôt GitHub.
- Vérifie avant de poster que le lien est bien public (teste-le en navigation privée) et qu'il pointe vers la racine du dépôt, pas vers un fichier ou une branche spécifique.
- Un seul commentaire par apprenant suffit ; si tu push des corrections après coup, un commentaire de mise à jour avec "edit" en préfixe évite la confusion sur quelle version évaluer.

---

## Checklist finale avant de considérer le projet terminé

- [ ] `.env` est listé dans `.gitignore` et n'apparaît jamais dans l'historique Git.
- [ ] `README.md` est à jour et permet une prise en main autonome.
- [ ] Le code ne contient pas de valeurs codées en dur qui devraient être des paramètres (seuils, chemins, dates).
- [ ] Les erreurs possibles (réseau, données manquantes, fichier introuvable) sont gérées explicitement, pas ignorées.
- [ ] Le dépôt ne contient pas de fichiers temporaires, de notebooks avec des sorties non nettoyées, ou de données brutes volumineuses versionnées par erreur.