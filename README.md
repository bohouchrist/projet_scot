# 🌾 Analyse des Vulnérabilités Foncières du SCoT Rhône Provence Baronnies (RPB)

> **Projet académique** — Master 2 Mathématiques Appliquées | Université Gustave Eiffel | 2025–2026

---

## 📝 Description du Projet

Ce projet vise à **évaluer et classifier le potentiel foncier agricole** sur le territoire du SCoT Rhône Provence Baronnies (RPB). En croisant des données géographiques, agronomiques, climatiques et d'occupation des sols, une **typologie des Unités Foncières (UF)** est établie grâce à un modèle de mélange gaussien (GMM), permettant d'identifier :

- Les zones à **fort potentiel agricole** à préserver
- Les zones sous **forte pression d'artificialisation** (vulnérabilités)

L'objectif est de fournir aux acteurs du territoire (aménageurs, collectivités, chambre d'agriculture) une vision structurée du foncier non bâti pour orienter les décisions de développement agricole et de préservation des milieux naturels.

---

## 👥 Auteurs

| Nom | Rôle |
|-----|------|
| **Loïc MONDOR** | Analyste géospatial |
| **Christ David BOHOU** | Data Scientist — modélisation & clustering |
| **Vaithehie SOMASUNTHARAM** | Analyse & traitement des données |

---

## 📁 Structure du Répertoire

```
.
├── consolidation/                         # Dossier données brutes et intermédiaires
│   ├── unites_foncieres_score_estime.gpkg     # ⚠️ Non inclus (taille > limite GitHub)
│   ├── TUP_score_agroclimatique.gpkg          # ⚠️ Non inclus (taille > limite GitHub)
│   ├── RPG_IQE_SCOT_RPB.gpkg                  # ⚠️ Non inclus (taille > limite GitHub)
│   └── Bati_SCoT_RPB.gpkg                     # ⚠️ Non inclus (taille > limite GitHub)
│
├── consolidation_ai_clean.ipynb           # 📓 Notebook principal — pipeline complet
├── carte_typologie_gmm.html               # 🗺️ Carte interactive Folium
├── graphique_bic_aic.png                  # 📊 Sélection du nombre de clusters
├── profils_par_groupe.png                 # 📊 Profils des groupes GMM
├── centres_clusters_gmm.png               # 📊 Centres des clusters normalisés
├── Rapport Final Cas d'usage.pdf          # 📄 Rapport de projet complet
└── README.md                              # Ce fichier
```

> ⚠️ **Note importante** : Les fichiers `.gpkg` (données géospatiales brutes) ne sont **pas inclus dans ce dépôt** en raison de leur taille importante (plusieurs centaines de Mo chacun). Ils sont disponibles en téléchargement via le **Google Drive du projet** (voir section ci-dessous).

---

## ☁️ Accès aux Données (Google Drive)

Les fichiers `.gpkg` nécessaires à l'exécution du notebook sont hébergés sur Google Drive :

🔗 **[Accéder au dossier Google Drive du projet](https://drive.google.com/drive/folders/1GEpvjQkEF8arQzRqA-QK0xAu8G8qzEsN?usp=drive_link)**

| Fichier | Description | Taille approx. |
|---------|-------------|----------------|
| `unites_foncieres_score_estime.gpkg` | Données Sol — 273 630 UF, score agronomique estimé | > 500 Mo |
| `TUP_score_agroclimatique.gpkg` | Données Climat — score agroclimatique par UF | > 500 Mo |
| `RPG_IQE_SCOT_RPB.gpkg` | Données RPG-PAC — IQE moyen et surface agricole | Variable |
| `Bati_SCoT_RPB.gpkg` | Données Bâti — bâtiments du territoire SCoT RPB | Variable |

**Instructions :**
1. Télécharger les fichiers depuis le Drive
2. Les placer dans le dossier `consolidation/` à la racine du projet
3. Lancer le notebook `consolidation_ai_clean.ipynb`

---

## ⚙️ Méthodologie

Le pipeline d'analyse combine traitement géospatial et apprentissage non supervisé :

```
Données Sol (.gpkg)
        +                  ──► Fusion par idtup ──► Super-Base ──► Enrichissement RPG-PAC
Données Climat (.gpkg)                                                       │
                                                                             ▼
                                                              Calcul Distance au Bâti
                                                                             │
                                                                             ▼
                                                             Normalisation [0,1] (5 variables)
                                                                             │
                                                                             ▼
                                                              GMM — sélection BIC/AIC (k=6)
                                                                             │
                                                                             ▼
                                                              Carte interactive + Rapport
```

### Étapes détaillées

1. **Chargement & Exploration** des couches géospatiales Sol et Climat (273 630 UF chacune)
2. **Fusion Sol + Climat** par jointure sur l'identifiant `idtup`
3. **Intégration RPG-PAC** : calcul de l'IQE moyen et du pourcentage agricole par UF via jointure spatiale
4. **Distance au Bâti** : exclusion du bâti agricole, calcul de `DIST_BATI_KM` (distance au bâti urbain le plus proche)
5. **Normalisation** des 5 variables clés entre 0 et 1
6. **Modélisation GMM** : sélection du nombre de clusters par critères BIC/AIC, puis classification finale

---

## 📊 Résultats — Typologie GMM (6 Groupes)

| Groupe | Nom de la Typologie | Caractéristiques |
|:------:|---------------------|------------------|
| **0** | 🟢 **Sanctuaire Agricole Premium** *(À PROTÉGER)* | Score agronomique & distance au bâti élevés, fort % agricole |
| **1** | 🔴 **Friches potentielles** *(Vulnérabilité TRÈS FORTE)* | Très faible % agricole, score faible, proximité du bâti |
| **2** | 🟠 **Cœur urbain en surchauffe** | Très faible potentiel agricole, très forte proximité du bâti |
| **3** | 🟡 **Ceinture agricole sous pression** | % agricole modéré-élevé, mais forte proximité du bâti |
| **4** | ⚪ **Tissu résidentiel & commercial standard** | Potentiel agricole très faible, zone fortement urbanisée |
| **5** | 🔵 **Espaces naturels & forestiers** | Score agronomique élevé, faible % agricole, éloigné du bâti |

---

## 🛠️ Installation & Dépendances

### Prérequis

- Python ≥ 3.9
- Jupyter Notebook ou JupyterLab

### Installation des dépendances

```bash
pip install geopandas pandas numpy scikit-learn matplotlib folium
```

> 💡 **Recommandation** : Utiliser un environnement conda pour la gestion de `geopandas` et ses dépendances géospatiales (GDAL, Fiona).

```bash
conda create -n scot-rpb python=3.10
conda activate scot-rpb
conda install -c conda-forge geopandas
pip install scikit-learn matplotlib folium
```

---

## ▶️ Exécution

1. **Cloner le dépôt** (ou télécharger l'archive)
2. **Télécharger les données** `.gpkg` depuis le [Google Drive](https://drive.google.com/drive/folders/1GEpvjQkEF8arQzRqA-QK0xAu8G8qzEsN?usp=drive_link) et les placer dans `consolidation/`
3. **Lancer le notebook** :
   ```bash
   jupyter notebook consolidation_ai_clean.ipynb
   ```
4. **Exécuter les cellules séquentiellement** — le pipeline est entièrement commenté

> ⏱️ Temps d'exécution estimé : **20–40 minutes** selon la machine (traitements géospatiaux lourds sur 273K+ lignes)

---

## 📄 Rapport

Le rapport complet (`Rapport Final Cas d'usage.pdf`) décrit :
- Le territoire du SCoT RPB et ses enjeux agricoles
- Les besoins exprimés par les partenaires
- La démarche de construction du modèle
- L'interprétation des résultats et les recommandations

---

## 📜 Licence

Projet académique — Université Gustave Eiffel | 2025–2026. Usage non commercial.
