# Projet 1 — Pipeline API : NYC TLC Yellow Taxi

🚧 **Statut : brief et barème de validation publiés — code et script de validation à venir.**

Premier projet d'une série de 7 pensée pour apprendre le Data Engineering en construisant, pas seulement en lisant. Celui-ci consiste à bâtir un pipeline complet — extraction, transformation, validation — à partir de l'API publique NYC TLC (New York City Taxi and Limousine Commission), sur les données des courses de taxis jaunes.

## Pourquoi ce projet

La plupart des tutoriels pipeline API utilisent un fichier déjà propre, téléchargé une fois pour toutes. Ici, c'est l'inverse : une vraie API avec pagination, authentification, données qui arrivent brutes et pas toujours cohérentes. L'objectif n'est pas d'obtenir un fichier qui a l'air correct, mais de savoir expliquer chaque décision prise pour y arriver.

## Ce que tu vas construire

Un pipeline automatisé qui, pour un mois donné :
1. Extrait l'intégralité des trajets depuis l'API Socrata
2. Nettoie, type correctement, et documente chaque anomalie détectée
3. Valide le résultat par une preuve chiffrée, pas une impression

👉 Le détail complet (prérequis, consignes, pièges à anticiper, barème de validation) est dans [`docs/consignes.md`](docs/consignes.md).

## Série complète

Ce projet fait partie d'une série de 7 : Pipeline API, Data Warehouse, Pipeline Cloud, Airflow, Big Data Spark, Streaming, et un projet End-to-End final.

## À venir

- Code de référence du pipeline
