# Projet : Clustering des Trajectoires GPS
## Optimisation des Déplacements avec Hadoop et Mahout

## Description
Ce projet implémente des techniques de clustering sur les données géospatiales du dataset **Geolife Trajectories** en utilisant Hadoop et Mahout. L'objectif est de regrouper des trajectoires en fonction de leur similarité, en exploitant des algorithmes comme **Canopy**, **K-Means** et **Fuzzy K-Means**.

---

## Structure du Répertoire
- **/projet/geolife_data** : Données brutes chargées dans HDFS.
- **/projet/geolife_seq** : Données converties au format séquentiel.
- **/projet/geolife_vectors** : Représentation vectorielle des données (TF-IDF).
- **/projet/Gio-Canopy** : Résultats du clustering Canopy.
- **/projet/Gio-Kmeans** : Résultats du clustering K-Means.
- **/projet/Gio-FuzzyKMeans** : Résultats du clustering Fuzzy K-Means.
- **/résultat/** : Résultats finaux des clusters.

---

## Instructions d'Exécution

### 1. Créer le répertoire HDFS et charger les données
```bash
hdfs dfs -mkdir -p /projet/geolife_data
hdfs dfs -put ~/Geolife_Trajectories21/Data/* /projet/geolife_data
```

### 2. Convertir les données au format séquentiel
```bash
mahout seqdirectory -i /projet/geolife_data -o /projet/geolife_seq -xm sequential
```

### 3. Générer les vecteurs TF-IDF
```bash
mahout seq2sparse -i /projet/geolife_seq -o /projet/geolife_vectors -ow -ng 1 -n 2
```

### 4. Exécuter l'algorithme Canopy
```bash
mahout canopy -i /projet/geolife_vectors/tf-vectors -o /projet/Gio-Canopy \
-dm org.apache.mahout.common.distance.CosineDistanceMeasure -t1 1500 -t2 2000
```

### 5. Clustering avec K-Means
```bash
mahout kmeans -i /projet/geolife_vectors/tfidf-vectors -c /projet/Gio-Canopy \
-o /projet/Gio-Kmeans -dm org.apache.mahout.common.distance.CosineDistanceMeasure \
--clustering -cl -cd 0.1 -x 20 -k 5
```

### 6. Extraire les résultats des clusters K-Means
```bash
mahout clusterdump -i /projet/Gio-Kmeans/clusters-2-final -o résultat/Gio-Results \
-p /projet/Gio-Kmeans/clusteredPoints -dt sequencefile -n 20
```

### 7. Clustering avec Fuzzy K-Means
```bash
mahout fkmeans -i /projet/geolife_vectors/tfidf-vectors -c /projet/Gio-Canopy \
-o /projet/Gio-FuzzyKMeans -dm org.apache.mahout.common.distance.CosineDistanceMeasure \
--clustering -cl -cd 0.1 -x 20 -k 5 --m 2.0
```

### 8. Extraire les résultats des clusters Fuzzy K-Means
```bash
mahout clusterdump -i /projet/Gio-FuzzyKMeans/clusters-2-final -o Resultats/Gio-Results1 \
-p /projet/Gio-FuzzyKMeans/clusteredPoints -dt sequencefile -n 20
```

### 9. Clustering alternatif avec K-Means
```bash
mahout kmeans -i /projet/geolife_vectors/tfidf-vectors -o /projet/Kmeans \
-dm org.apache.mahout.common.distance.CosineDistanceMeasure -k 5 -x 20 -cd 0.1 \
--clustering --clusters /Kmeans/clusters
```

### 10. Extraire les résultats finaux de K-Means
```bash
mahout clusterdump -i /projet/Kmeans/clusters-2-final -o Resultats/Gio-Results2 \
-p /projet/Kmeans/clusteredPoints -dt sequencefile -n 20
```

---

## Résultats
Les résultats finaux sont générés dans les répertoires suivants :
- **résultat/** : Contient les sorties du clustering K-Means.
- **Resultats/** : Contient les sorties des clusters Fuzzy K-Means.

---

## Auteurs
- [STAIFI Fahd]
- [NAHID Doha]
