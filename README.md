# 🍎 Déploiement d'une architecture Big Data sur le Cloud (AWS EMR)

![Python](https://img.shields.io/badge/Python-3.10+-3776ab?style=flat)
![Library](https://img.shields.io/badge/Library-PySpark-e25a1c?style=flat)
![Cloud](https://img.shields.io/badge/Cloud-AWS_EMR_%7C_S3-ff9900?style=flat)
![Model](https://img.shields.io/badge/Model-MobileNetV2-8a2be2?style=flat)
![Task](https://img.shields.io/badge/Task-Feature_Extraction_%7C_PCA-4caf50?style=flat)

Ce dépôt contient mon travail pour le Projet 9 du parcours Data Scientist d'OpenClassrooms. L'objectif est de mettre en place une première architecture Big Data sur le Cloud AWS pour traiter massivement des images de fruits.

## 📝 Contexte du Projet

Je travaille pour "Fruits!", une start-up de l'AgriTech qui souhaite développer une application mobile de reconnaissance de fruits pour préserver la biodiversité. 
Face à l'augmentation rapide du volume de données (images), l'enjeu de ce projet est de construire une chaîne de traitement Big Data capable de passer à l'échelle (scalabilité) pour extraire les caractéristiques (features) de ces images de manière distribuée.

## 🎯 Objectifs

* Mettre en place un environnement Big Data fonctionnel sur le Cloud (AWS EMR, S3).
* Développer un script PySpark pour charger et traiter des images de manière distribuée.
* Appliquer une technique de Transfer Learning (MobileNetV2) pour l'extraction de features.
* Optimiser les calculs distribués en diffusant les poids du modèle (Broadcast).
* Réaliser une réduction de dimension (PCA) avec PySpark ML.
* Respecter les contraintes RGPD en déployant l'infrastructure sur des serveurs européens.

## 🛠️ Technologies et Outils

* **Langage :** Python
* **Big Data :** PySpark, Apache Spark
* **Cloud Computing :** AWS (EMR, S3)
* **Machine Learning / Deep Learning :** TensorFlow, Keras (MobileNetV2), PySpark MLlib (PCA)
* **Manipulation de données :** Pandas, Numpy, PIL

## 🧠 Méthodologie et Pipeline de Traitement

1. **Stockage des données :** Hébergement des images brutes sur le service de stockage objet AWS S3.
2. **Initialisation Spark :** Démarrage d'une session Spark configurée sur un cluster AWS EMR.
3. **Chargement des images :** Lecture distribuée des fichiers images `.jpg` directement depuis le bucket S3 et extraction des labels à partir de l'arborescence.
4. **Modélisation (Transfer Learning) :** * Chargement du modèle pré-entraîné MobileNetV2.
   * Amputation de la dernière couche de classification pour l'utiliser comme extracteur de caractéristiques.
   * **Optimisation :** Diffusion (Broadcast) des poids du modèle sur tous les nœuds du cluster (Workers) pour éviter les transferts réseaux redondants.
5. **Extraction des features :** Utilisation de `pandas_udf` (User Defined Functions) pour appliquer le modèle Keras de manière vectorisée et distribuée.
6. **Réduction de dimension :** Entraînement et application d'une Analyse en Composantes Principales (PCA) via `pyspark.ml`.
7. **Sauvegarde :** Écriture des résultats finaux au format Parquet sur S3.

## 📊 Résultats obtenus

Le pipeline de traitement distribué a été exécuté avec succès sur le cluster AWS EMR :

* **Volume traité :** Le script a traité un total de **22 688 images** de fruits réparties dans plusieurs catégories (ex: Watermelon, Pineapple Mini, etc.).
* **Extraction de features :** Le passage des images dans le modèle MobileNetV2 a généré un vecteur dense de **1280 dimensions** pour chaque image.
* **Réduction de dimension (PCA) :** Afin d'optimiser le stockage et les futurs temps d'entraînement, la PCA a permis de réduire ces vecteurs de 1280 dimensions à seulement **50 composantes principales** (k=50).
* **Export :** Le DataFrame final, contenant le chemin S3 de l'image, son label, et le vecteur de features réduit, a été sauvegardé avec succès au format **Parquet** (`s3://.../Results_PCA`), prêt à être ingéré par un futur modèle de classification.

## 🚀 Instructions d'Exécution

Pour reproduire ce projet, vous devez disposer d'un compte AWS actif.

1. Créez un bucket S3 et uploadez le jeu de données d'images.
2. Configurez vos rôles IAM pour autoriser EMR à accéder à S3.
3. Lancez un cluster AWS EMR avec les applications Spark et Livy (pour les notebooks Jupyter). *Veillez à choisir une région européenne (ex: `eu-west-3` pour Paris) pour être conforme au RGPD.*
4. Modifiez les variables `PATH`, `PATH_Data` et `PATH_Result` dans le notebook pour les faire correspondre à l'URI de votre bucket S3.
5. Exécutez le notebook `Projet 9 cloud.ipynb` cellule par cellule.

> **⚠️ Avertissement Cloud :** Pensez à résilier (terminate) votre cluster EMR dès la fin de vos traitements pour éviter des coûts de facturation inutiles.
