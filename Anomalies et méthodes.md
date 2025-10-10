# Liste complète des anomalies et choix méthodologiques

## Anomalies détectées

### Démographie
- **NetMigrations** : Valeurs négatives fréquentes au Bénin (post-1990) et en Afrique de l’Ouest, indiquant une émigration nette.
- **IMR** : Taux de mortalité infantile élevé en 1990 (100-150 décès/1000 naissances), avec forte baisse après.
- **Q5** : Mortalité des moins de 5 ans élevée en 1990, diminuant rapidement.
- **PopTotal** : Ralentissement de la croissance en 2020 (possible impact COVID).
- **MedianAgePop** : Stagnation autour de 18 ans en Afrique de l’Ouest, malgré la croissance démographique.

### Social
- **mys** : Stagnation des années moyennes de scolarité post-2015 au Bénin (sous 4 ans).
- **gii** : Inégalités de genre élevées avant 2000 (autour de 0.7), avec réduction lente.
- **gnipc** : Volatilité post-2020, probablement liée à COVID.
- **gdi** : Données manquantes pour certaines années avant 2000, limitant l’analyse précoce de l’égalité de genre.
- **hdi** : Valeurs manquantes sporadiques pour l’Afrique subsaharienne avant 1995, affectant HDI_Per_TFR.

### FDI
- **FDI_Current** : Valeurs négatives en Outward (ex. Afrique de l’Ouest 2022, Afrique subsaharienne 2023), indiquant des désinvestissements.
- **FDI_Stock** : Données manquantes pour Stock Inward au Bénin avant 1992.
- **FDI_Current** : Pic en Inward pour l’Afrique subsaharienne en 2021 (post-COVID).
- **FDI_Net** : Valeurs négatives possibles pour certains pays et années (ex. Bénin 2022), impactant FDI_Per_Person.

### GDP
- **GDP_Current_PerCapita** : Chute en 2020 (impact COVID).
- **GDP_Current** : Volatilité post-2015 au Bénin (fluctuations monétaires).
- **GDP_Constant_PerCapita** : Croissance plus lente au Bénin vs Afrique subsaharienne.
- **GDP_Constant** : Valeurs stables mais avec des écarts importants entre Bénin et Afrique subsaharienne, nécessitant des axes secondaires.

### Croisements
- **FDI_Per_Person** : Valeurs négatives possibles si FDI_Net est négatif (désinvestissements).
- **GDP_Per_Density** : Valeurs faibles au Bénin (urbanisation limitée), avec volatilité en 2020 (COVID).
- **HDI_Per_TFR** : Variations dues à la baisse de TFR, amplifiant hdi (ex. Bénin post-2000).
- **LEx_vs_GDP_Constant_PerCapita** : Valeurs élevées si GDP_Constant_PerCapita faible (ex. Bénin pré-2000).
- **PopSexRatio_vs_GDI** : Variations dues à la stabilité de PopSexRatio et hausse de gdi.
- **GII_vs_IMR** : Valeurs élevées en 1990 (haut gii, haut IMR), diminuant avec les progrès sociaux.
- **GII_vs_IMR** : Données manquantes pour gii avant 2000 dans certaines régions, réduisant les points de données pour ce croisement.
- **PopSexRatio_vs_GDI** : Valeurs potentiellement instables si gdi proche de 0 (bien que rare, vérifié par filtrage des valeurs non-nulles).

## Choix méthodologiques

### Chargement des données
- Fichiers CSV chargés avec `pd.read_csv`, un par un, pour optimiser la mémoire.
- Renommage des colonnes pour uniformité (ex. `Location` → `Country`, `country` → `Country`).
- Filtrage sur Bénin, Afrique subsaharienne, Afrique de l’Ouest, et 1990-2023 pour cohérence.
- Conversion des années en type numérique pour `social_data.csv` (via `str.extract`) pour alignement avec autres datasets.

### Préparation des données
- `social_data.csv` transformé en format long (via `melt`) pour `hdi`, `gdi`, `gii`, puis pivoté pour éviter les doublons.
- `fdi_data.csv` agrégé pour `FDI_Net` (Inward - Outward) avant fusion.
- Gestion des valeurs manquantes avec `dropna` pour les indicateurs sociaux.
- Utilisation de `pivot_table` pour `social_data.csv` afin de regrouper `hdi`, `gdi`, `gii` par `Year` et `Country`, évitant les doublons lors des fusions.
- Filtrage des valeurs non-nulles avant calcul des croisements pour éviter les erreurs de division par zéro (ex. `gdi` dans `PopSexRatio_et_GDI`).

### Nouvelles variables
- `Net_FDI` : `FDI_Current` (Inward) - `FDI_Current` (Outward).
- `FDI_Per_Person` : `FDI_Net / PopTotal`.
- `GDP_Per_Density` : `GDP_Constant_PerCapita / PopDensity`.
- `HDI_Per_TFR` : `hdi / TFR`.
- `LEx_et_GDP_Constant_PerCapita` : `LEx / GDP_Constant_PerCapita`.
- `PopSexRatio_et_GDI` : `PopSexRatio / gdi`.
- `GII_et_IMR` : `gii / IMR`.
- Calculées à la volée, supprimées après usage.
- Vérification des dénominateurs non-nuls avant calcul des ratios (ex. `PopDensity`, `TFR`, `gdi`, `IMR`) pour éviter les erreurs.

### Analyse des anomalies
- Utilisation de **lineplots** pour visualiser les tendances temporelles des variables par pays (ex. `PopTotal`, `FDI_Per_Person`), permettant d’identifier visuellement les variations inhabituelles.
- Utilisation de **boxplots** pour détecter les valeurs extrêmes et les distributions par pays, mettant en évidence les outliers et les valeurs négatives potentielles.
- Calcul des **statistiques descriptives** (moyenne, médiane, min, max, etc.) pour chaque variable, regroupées par pays, afin de comprendre la dispersion et les tendances.
- Détection des **valeurs fausses** via des conditions spécifiques (ex. `PopTotal < 0`, `LEx > 100`, `hdi` hors [0, 1]) pour identifier les erreurs logiques.
- Détection des **outliers** avec le Z-score (valeurs avec |Z| > 3), permettant de repérer les valeurs inhabituelles qui peuvent refléter des erreurs ou des événements réels (ex. chute du PIB en 2020).
- Sortie : Résultats (stats, valeurs fausses, outliers) affichés dans la console ou les cellules Jupyter, avec graphiques sauvegardés en PNG dans `anomalies_graphs/` (ex. `demo_PopTotal.png`, `demo_PopTotal_boxplot.png`).

### Visualisation
- Axes secondaires sélectifs : Pour `PopTotal`, `NetMigrations`, `FDI_Current`, `FDI_Percentage`, `GDP_Constant`, `GDP_Current`, et tous les croisements.
- Axe unique : Pour les autres variables (ex. `hdi`, `PopSexRatio`) via `sns.lineplot`.
- Échelle logarithmique : Si max/min > 100, appliquée indépendamment pour chaque axe.
- Unités : Axes Y étiquetés avec le glossaire (ex. "Années par USD par personne" pour `LEx_et_GDP_Constant_PerCapita`).
- Sortie : Graphiques en PNG (`graphs/`) et affichés dans le Notebook (`plt.show()`).
- Utilisation de couleurs distinctes (orange, bleu) pour les axes secondaires, adaptées aux thèmes clair et sombre.

### Insights
- Phrases simples pour le grand public, sans chiffres, anomalies, ni recommandations.
- Écrits dans `insights.md` pour chaque variable et croisement, sans inclure `{feature}_Growth` pour respecter les instructions.
- Ordre des insights aligné sur les sections : démographie, social, FDI, GDP, croisements.

### Croisements
- Fusions sur `Year` et `Country` avec `how='inner'` pour données cohérentes.
- Axes secondaires pour gérer les grands écarts (ex. Bénin vs Afrique subsaharienne).
- Gestion des données manquantes dans les croisements par suppression des lignes incomplètes après fusion, assurant des calculs fiables.
- Ordre des croisements dans `insights.md` : `FDI_Per_Person`, `GDP_Per_Density`, `HDI_Per_TFR`, `LEx_et_GDP_Constant_PerCapita`, `PopSexRatio_et_GDI`, `GII_et_IMR`.

### Optimisation mémoire
- DataFrames supprimés après chaque analyse (`del df`).
- Graphiques affichés et sauvegardés sans surcharge RAM.
- Chargement séquentiel des fichiers CSV pour limiter l’empreinte mémoire, avec libération immédiate des DataFrames temporaires.