# JupyterLab + PySpark — NYC Taxi Trip Duration

Travaux pratiques du module **5SPAR** (Apache Spark & traitement distribué) — SUPINFO Paris.

Analyse du dataset *NYC Taxi Trip Duration* avec PySpark dans un environnement JupyterLab conteneurisé avec Docker.

## Stack

- Docker / Docker Compose
- JupyterLab
- Apache Spark (PySpark)

## Structure du projet

```
jupyter1/
├── docker-compose.yml
├── notebooks/
│   ├── *.ipynb                      # notebooks des exercices
│   └── nyc-taxi-trip-duration/      # dataset (non versionné)
├── .gitignore
└── README.md
```

## Récupérer le dataset

Le dataset n'est pas inclus dans le dépôt (fichier > 100 Mo).

1. Télécharger les données sur Kaggle : [NYC Taxi Trip Duration](https://www.kaggle.com/c/nyc-taxi-trip-duration/data)
2. Décompresser `train.zip` dans `notebooks/nyc-taxi-trip-duration/`

Chemin attendu : `notebooks/nyc-taxi-trip-duration/train.csv`

## Lancer l'environnement

```bash
git clone https://github.com/Coulibaly-mohamed/jupyterLab.git
cd jupyterLab
docker compose up -d
```

Ouvrir JupyterLab dans le navigateur (le lien avec le token s'affiche avec `docker compose logs`).

Si les données ont été copiées manuellement dans le conteneur :

```bash
docker cp ./notebooks/nyc-taxi-trip-duration <container_id>:/home/jovyan/work/
```

## Schéma des données

| Colonne | Type | Description |
|---|---|---|
| id | string | Identifiant du trajet |
| vendor_id | integer | Fournisseur |
| pickup_datetime | timestamp | Date/heure de prise en charge |
| dropoff_datetime | timestamp | Date/heure de dépose |
| passenger_count | integer | Nombre de passagers |
| pickup_longitude / pickup_latitude | double | Coordonnées de départ |
| dropoff_longitude / dropoff_latitude | double | Coordonnées d'arrivée |
| store_and_fwd_flag | string | Trajet stocké avant envoi (Y/N) |
| trip_duration | integer | Durée du trajet (secondes) |

## Exercices réalisés

**Manipulation de DataFrames**
- Chargement du CSV avec inférence du schéma
- Filtrage : trajets de plus de 2 passagers et de moins de 10 minutes
- Création de `trip_duration_in_minutes`, cast de `vendor_id` en string, tri décroissant
- Fusion de DataFrames avec `union` / `unionByName`

**Optimisation Spark**
- Durée moyenne des trajets par jour (`trip_date`, `groupBy`, `avg`)
- Comparaison des temps d'exécution : sans repartitionnement, `repartition`, `coalesce`
- Mise en cache (`cache`) et analyse des plans d'exécution (`explain`)

## Exemple

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("NYC Taxi").getOrCreate()

df = spark.read.csv(
    "/home/jovyan/work/nyc-taxi-trip-duration/train.csv",
    header=True,
    inferSchema=True
)
df.printSchema()
```

## Auteur

Mohamed Coulibaly — Master Informatique, spécialisation Data & IA, SUPINFO Paris
