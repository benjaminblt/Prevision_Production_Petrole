# Production de pétrole en Thaïlande : stationnarité et prévisions ARIMA

![R](https://img.shields.io/badge/R-Analyse%20statistique-276DC3?logo=r&logoColor=white)
![Séries temporelles](https://img.shields.io/badge/Séries%20temporelles-Économétrie-1F4E79)
![ARIMA](https://img.shields.io/badge/Modélisation-ARIMA-2E8B57)
![Prévisions](https://img.shields.io/badge/Prévisions-2022--2025-D8A633)
![Statut](https://img.shields.io/badge/Statut-Terminé-2E8B57)

Projet académique en deux volets complémentaires consacré à l’analyse économétrique et à la prévision de la **production de pétrole en Thaïlande**.

L’étude porte sur une série annuelle allant de **1971 à 2021**.

Le premier projet étudie la stationnarité de la série et recherche la présence de racines unitaires et de ruptures structurelles.

Le second projet prolonge cette analyse par la sélection d’un modèle ARIMA et la réalisation de prévisions pour les années 2022 à 2025.

> Projet réalisé par Benjamin Baillet dans le cadre du Master IREF-FQA à l’Université de Bordeaux.

---

## Sommaire

- [Vue d’ensemble](#vue-densemble)
- [Articulation des deux projets](#articulation-des-deux-projets)
- [Objectifs](#objectifs)
- [Données](#données)
- [Projet 1 - Racines unitaires](#projet-1---racines-unitaires)
- [Projet 2 - Prévisions ARIMA](#projet-2---prévisions-arima)
- [Résultats principaux](#résultats-principaux)
- [Prévisions obtenues](#prévisions-obtenues)
- [Méthodologie](#méthodologie)
- [Outils et packages](#outils-et-packages)
- [Structure du dépôt](#structure-du-dépôt)
- [Reproduire le projet](#reproduire-le-projet)
- [Compétences démontrées](#compétences-démontrées)
- [Limites](#limites)
- [Pistes d’amélioration](#pistes-damélioration)
- [Auteur](#auteur)

---

# Vue d’ensemble

Les deux projets forment une étude progressive de la production de pétrole en Thaïlande.

```text
Données annuelles de 1971 à 2021
                 ↓
Visualisation de la série temporelle
                 ↓
Projet 1 : tests de racines unitaires
                 ↓
Détection des ruptures structurelles
                 ↓
Différenciation de la série à l’ordre 1
                 ↓
Projet 2 : identification d’un modèle ARIMA
                 ↓
Tests des coefficients et des résidus
                 ↓
Sélection du meilleur modèle par le BIC
                 ↓
Prévisions de 2022 à 2025
```

Les analyses sont réalisées avec le langage **R**.

Les résultats sont présentés sous forme :

- de scripts R ;
- de graphiques ;
- de tests statistiques ;
- de modèles économétriques ;
- de présentations PowerPoint.

---

# Articulation des deux projets

## Projet 1

Le premier projet étudie les propriétés de la série historique de production de pétrole.

Il comprend :

- l’analyse du chronogramme ;
- l’étude de la tendance ;
- l’étude de l’hétéroscédasticité ;
- l’analyse des fonctions ACF et PACF ;
- le test de Dickey-Fuller ;
- le test de Dickey-Fuller augmenté ;
- le test de Zivot-Andrews ;
- le test de Lee-Strazicich avec bootstrap ;
- la détection de ruptures structurelles ;
- la différenciation de la série ;
- la vérification de la stationnarité après différenciation.

## Projet 2

Le second projet reprend les conclusions du premier projet.

Il comprend :

- l’étude de la saisonnalité ;
- la différenciation de la série ;
- l’analyse de l’ACF et de la PACF ;
- l’utilisation de l’EACF ;
- l’estimation de modèles ARIMA ;
- les tests de significativité des coefficients ;
- l’analyse des résidus ;
- la comparaison de plusieurs modèles ;
- la sélection du meilleur modèle avec le BIC ;
- la production de prévisions jusqu’en 2025.

---

# Objectifs

L’étude cherche à répondre aux questions suivantes :

- La production de pétrole présente-t-elle une tendance ?
- La variance de la série est-elle constante ?
- La série comporte-t-elle une saisonnalité ?
- La série est-elle stationnaire ?
- Existe-t-il une racine unitaire ?
- Le processus générateur de données est-il déterministe ou stochastique ?
- La série présente-t-elle des ruptures structurelles ?
- Quelles années correspondent aux principales ruptures ?
- Combien de différenciations sont nécessaires pour stationnariser la série ?
- Quels ordres `p` et `q` doivent être utilisés dans un modèle ARIMA ?
- Les coefficients estimés sont-ils significatifs ?
- Les résidus du modèle sont-ils des bruits blancs ?
- Quel modèle possède le BIC le plus faible ?
- Quelles prévisions peut-on obtenir pour les années 2022 à 2025 ?

---

# Données

## Série étudiée

```text
Pays : Thaïlande
Code pays : THA
Période : 1971-2021
Fréquence : annuelle
Variable : production de pétrole
Unité utilisée dans le projet : milliers de tonnes d’équivalent pétrole
```

Le fichier de données contient notamment les colonnes :

| Colonne | Description |
|---|---|
| `LOCATION` | Code du pays |
| `TIME` | Année d’observation |
| `Value` | Valeur de la production |

Les données de la Thaïlande sont sélectionnées avec :

```r
tab2 <- subset(tab, select = c(LOCATION, TIME, Value))

thailande <- tab2[tab2$LOCATION == "THA", ]

thailande <- na.omit(thailande)
```

La série temporelle annuelle est ensuite créée avec :

```r
Petrole <- ts(
  na.omit(thailande[, "Value"]),
  start = 1971,
  frequency = 1
)
```

---

# Projet 1 - Racines unitaires

## Visualisation de la série

Le chronogramme de la production de pétrole montre :

- une tendance globale croissante ;
- une variance qui évolue dans le temps ;
- une absence de saisonnalité annuelle observable.

La droite de tendance est estimée avec une régression linéaire :

```r
ggplot(thailande, aes(x = TIME, y = Value)) +
  geom_line() +
  geom_smooth(
    method = "lm",
    col = "red",
    se = FALSE
  )
```

La variation de l’écart à la tendance est interprétée dans le projet comme une forme d’hétéroscédasticité.

Une décomposition multiplicative est donc étudiée.

---

## ACF et PACF

Les fonctions d’autocorrélation permettent d’identifier une dépendance temporelle.

```r
Acf(Petrole, lag = 80)
Pacf(Petrole)
```

L’ACF présente une alternance de corrélations positives et négatives.

La PACF fait apparaître une autocorrélation importante aux premiers retards.

Ces résultats conduisent à utiliser un test de Dickey-Fuller augmenté afin de prendre en compte l’autocorrélation.

---

## Test de Dickey-Fuller

Le test de Dickey-Fuller est effectué avec trois spécifications successives :

1. tendance et constante ;
2. constante seule ;
3. sans tendance ni constante.

```r
ur.df(Petrole, type = "trend", lag = 0)
ur.df(Petrole, type = "drift", lag = 0)
ur.df(Petrole, type = "none", lag = 0)
```

Dans le cadre des résultats obtenus, l’hypothèse de racine unitaire n’est pas rejetée.

Cependant, la présence d’autocorrélation remet en cause la validité du test DF simple.

---

## Test de Dickey-Fuller augmenté

Le nombre maximal de retards est d’abord estimé avec la formule de Schwert :

```r
pmax <- as.integer(
  12 * (length(Petrole) / 100)^(0.25)
)
```

Le critère MAIC est ensuite utilisé pour sélectionner le nombre de retards.

```r
CADFtest(
  Petrole,
  criterion = "MAIC",
  type = "trend",
  max.lag.y = pmax
)
```

Le projet retient :

```text
3 retards
```

Les spécifications avec tendance, constante puis sans constante sont étudiées.

La conclusion retenue est que la série possède une racine unitaire.

Le processus générateur de données est donc considéré comme un processus **Difference Stationary**, ou DS.

---

## Test de Zivot-Andrews

Le test de Zivot-Andrews permet de tester la présence d’une racine unitaire en autorisant une rupture structurelle endogène.

Deux spécifications sont étudiées :

```r
ur.za(Petrole, model = "both", lag = pmax)
ur.za(Petrole, model = "intercept", lag = pmax)
```

La spécification avec rupture sur l’intercept détecte une rupture autour de :

```text
2000
```

Dans le projet, cette rupture est mise en relation avec une période de sécheresse ayant affecté la production.

L’hypothèse de racine unitaire reste néanmoins retenue.

---

## Test de Lee-Strazicich

Le test de Lee-Strazicich est utilisé avec bootstrap en raison du nombre limité d’observations.

Deux versions sont étudiées :

- une rupture ;
- deux ruptures.

Le modèle retenu est de type :

```text
crash
```

Il correspond à une rupture de niveau.

### Une rupture

Le test détecte une rupture autour de :

```text
2011
```

La conclusion du test est que la série est DS avec une rupture structurelle.

### Deux ruptures

Le test détecte deux ruptures autour de :

```text
2011
2016
```

Ces dates sont rapprochées dans le projet de différents événements économiques, pétroliers et géopolitiques.

---

## Conclusion sur la série initiale

Parmi les différents tests réalisés, le projet retient en priorité le test de Lee-Strazicich avec bootstrap.

La conclusion est la suivante :

```text
La série de production de pétrole est non stationnaire.
Elle possède une racine unitaire.
Le processus générateur de données est DS.
```

---

## Différenciation

La série est différenciée à l’ordre 1 :

```r
dPetrole <- diff(Petrole)
```

La différenciation permet de retirer la tendance stochastique.

Le projet conclut que la série différenciée devient stationnaire.

La série initiale est donc considérée comme intégrée d’ordre 1 :

```text
Petrole ~ I(1)
```

---

# Projet 2 - Prévisions ARIMA

## Absence de saisonnalité

Les données sont annuelles.

Le projet ne retient donc pas de saisonnalité intra-annuelle.

Aucune différenciation saisonnière n’est appliquée.

La seule différenciation utilisée correspond à la différenciation ordinaire d’ordre 1.

---

## Série différenciée

La série utilisée pour identifier les ordres autorégressifs et moyenne mobile est :

```r
dPetrole <- diff(Petrole)
```

Après différenciation :

- la tendance est retirée ;
- la série fluctue autour de zéro ;
- la stationnarité est considérée comme atteinte.

---

## Analyse ACF et PACF

Les fonctions ACF et PACF sont calculées sur la série différenciée :

```r
acf(dPetrole)
pacf(dPetrole)
```

Le projet observe notamment :

```text
ACF : autocorrélations aux ordres 3 et 15
PACF : autocorrélation à l’ordre 3
```

---

## EACF

L’EACF est utilisée pour proposer des valeurs initiales de `p` et `q`.

```r
eacf(dPetrole)
```

La première spécification étudiée est :

```text
p = 0
q = 3
d = 1
```

soit :

```text
ARIMA(0,1,3)
```

---

## Premier modèle ARIMA

Le modèle est estimé avec :

```r
reg_Petrole <- Arima(
  Petrole,
  order = c(0, 1, 3)
)
```

Les coefficients `ma1` et `ma2` ne sont pas significatifs dans l’estimation initiale.

Ils sont donc fixés à zéro :

```r
reg_Petrole <- Arima(
  Petrole,
  order = c(0, 1, 3),
  fixed = c(0, 0, NA)
)
```

Les coefficients restants deviennent significatifs.

---

## Tests des résidus

Un modèle est conservé uniquement lorsque ses résidus peuvent être considérés comme des bruits blancs.

Trois propriétés sont vérifiées.

### Espérance nulle

```r
t.test(reg_Petrole$residuals)
```

L’hypothèse d’une moyenne nulle des résidus n’est pas rejetée.

### Variance constante

```r
ArchTest(
  reg_Petrole$residuals,
  lag = 1
)
```

L’hypothèse d’absence d’effet ARCH n’est pas rejetée.

### Absence d’autocorrélation

```r
Box.test(
  reg_Petrole$residuals,
  lag = 1,
  type = "Ljung-Box"
)
```

L’hypothèse d’absence d’autocorrélation n’est pas rejetée.

Les résidus du modèle sont donc considérés comme des bruits blancs dans le cadre des tests réalisés.

---

## Comparaison des modèles

L’EACF fournit seulement une première indication.

Le projet compare ensuite 18 spécifications ARIMA faisant varier `p` et `q`.

Parmi les modèles étudiés figurent notamment :

```text
ARIMA(0,1,1)
ARIMA(0,1,2)
ARIMA(0,1,3)
ARIMA(0,1,4)
ARIMA(0,1,7)
ARIMA(1,1,1)
ARIMA(1,1,2)
ARIMA(1,1,3)
ARIMA(1,1,4)
ARIMA(2,1,1)
ARIMA(2,1,2)
ARIMA(2,1,3)
ARIMA(3,1,0)
ARIMA(3,1,1)
ARIMA(3,1,2)
ARIMA(4,1,0)
ARIMA(4,1,1)
ARIMA(5,1,0)
```

Pour chaque modèle, le projet vérifie :

1. la significativité des coefficients ;
2. l’espérance nulle des résidus ;
3. la constance de leur variance ;
4. l’absence d’autocorrélation ;
5. la valeur du BIC.

---

## Modèle final retenu

Le modèle présentant le BIC le plus faible est :

```text
ARIMA(3,1,2)
```

Il est donc retenu pour produire les prévisions principales.

Le modèle combine :

- trois composantes autorégressives ;
- une différenciation d’ordre 1 ;
- deux composantes moyenne mobile.

---

# Prévisions obtenues

Les prévisions sont réalisées sur la série initiale avec un horizon de quatre années :

```r
forecast(reg15, h = 4)
```

## Prévisions principales

| Année | Prévision |
|---|---:|
| 2022 | 8 786,733 |
| 2023 | 7 873,134 |
| 2024 | 7 127,349 |
| 2025 | 7 025,134 |

Les valeurs sont exprimées dans l’unité utilisée dans le projet, soit en milliers de tonnes d’équivalent pétrole.

Le graphique est créé avec :

```r
autoplot(
  forecast(reg15, h = 4)
) +
  labs(
    title = "Prévisions pour la Thaïlande de 2022 à 2025",
    x = "Années",
    y = "Production de pétrole"
  )
```

---

# Comparaison avec les valeurs observées

Le projet compare ensuite certaines prévisions avec les valeurs réelles ajoutées ultérieurement.

Les valeurs mentionnées sont :

```text
2022 : 8 555
2023 : 8 825
```

La prévision initiale pour 2022 est relativement proche de la valeur présentée comme réelle.

L’écart est plus important pour l’année 2023.

---

# Analyse complémentaire avec la donnée de 2022

Une analyse supplémentaire est réalisée en ajoutant la valeur observée de 2022 à la série :

```r
Petrole <- c(Petrole, 8555)
```

Les différentes étapes sont répétées :

- différenciation ;
- ACF ;
- PACF ;
- EACF ;
- estimation de plusieurs modèles ;
- tests des coefficients ;
- tests des résidus ;
- comparaison des BIC.

Le meilleur modèle devient alors :

```text
ARIMA(3,1,0)
```

## Nouvelles prévisions

| Année | Prévision mise à jour |
|---|---:|
| 2023 | 7 861,154 |
| 2024 | 7 109,445 |
| 2025 | 6 865,935 |

Cette analyse montre que l’ajout d’une nouvelle observation peut modifier :

- le modèle sélectionné ;
- ses paramètres ;
- les valeurs prévues.

---

# Résultats principaux

## Projet 1

- tendance globale croissante ;
- absence de saisonnalité ;
- variance évoluant dans le temps ;
- présence d’autocorrélation ;
- série initiale non stationnaire ;
- racine unitaire détectée ;
- processus générateur considéré comme DS ;
- ruptures détectées autour de 2000, 2011 et 2016 selon les tests ;
- série stationnaire après une différenciation ;
- série initiale considérée comme intégrée d’ordre 1.

## Projet 2

- différenciation ordinaire d’ordre 1 ;
- première indication EACF : ARIMA(0,1,3) ;
- comparaison de 18 modèles ;
- validation des résidus par trois familles de tests ;
- sélection finale d’un ARIMA(3,1,2) avec le BIC ;
- prévisions de 2022 à 2025 ;
- sélection d’un ARIMA(3,1,0) après ajout de l’observation 2022.

---

# Méthodologie

## Tests de racines unitaires

- Dickey-Fuller ;
- Dickey-Fuller augmenté ;
- sélection du nombre de retards ;
- formule de Schwert ;
- critère MAIC ;
- Zivot-Andrews ;
- Lee-Strazicich ;
- bootstrap ;
- ruptures structurelles.

## Identification des modèles

- chronogramme ;
- décomposition ;
- ACF ;
- PACF ;
- EACF ;
- différenciation ;
- modèles ARIMA.

## Validation

- test de Student ;
- test ARCH ;
- test de Ljung-Box ;
- significativité des coefficients ;
- analyse des résidus ;
- comparaison par le BIC.

## Prévisions

- prévisions multi-horizons ;
- intervalles de prévision ;
- comparaison avec des observations réelles ;
- réestimation après ajout d’une observation.

---

# Outils et packages

## Outils

- R ;
- RStudio ;
- Microsoft PowerPoint ;
- GitHub.

## Packages principaux

```r
library(ggplot2)
library(forecast)
library(caschrono)
library(lmtest)
library(urca)
library(CADFtest)
library(dplyr)
library(foreach)
library(doSNOW)
library(parallel)
library(FinTS)
library(TSA)
library(Hmisc)
```

### Utilisation des packages

| Package | Utilisation |
|---|---|
| `ggplot2` | Visualisation de la série |
| `forecast` | ARIMA et prévisions |
| `caschrono` | Analyse de séries temporelles |
| `lmtest` | Tests et coefficients |
| `urca` | Tests de racines unitaires |
| `CADFtest` | Dickey-Fuller augmenté |
| `FinTS` | Test ARCH |
| `TSA` | EACF |
| `foreach` | Boucles parallèles |
| `doSNOW` | Parallélisation |
| `parallel` | Utilisation de plusieurs cœurs |

---

# Structure du dépôt

```text
production-petrole-thailande-series-temporelles/
│
├── README.md
│
├── projet-1-racines-unitaires/
│   ├── Projet1_Baillet.R
│   └── Projet1_Baillet.pptx
│
├── projet-2-previsions-arima/
│   ├── Projet2_Baillet.R
│   └── Projet2_Baillet.pptx
│
├── donnees/
│   └── petrole.csv
│
└── documentation/
    └── synthese_projet_petrole_thailande.pdf
```

---

# Reproduire le projet

## Prérequis

- R ;
- RStudio ;
- fichier `petrole.csv`.

## Installer les packages

```r
install.packages(c(
  "ggplot2",
  "forecast",
  "caschrono",
  "lmtest",
  "urca",
  "CADFtest",
  "dplyr",
  "foreach",
  "doSNOW",
  "parallel",
  "FinTS",
  "TSA",
  "Hmisc"
))
```

Le package `parallel` est généralement inclus avec R.

---

## Organiser les fichiers

Pour faciliter l’exécution, placer temporairement dans un même dossier :

```text
Projet1_Baillet.R
Projet2_Baillet.R
petrole.csv
```

Dans RStudio :

```text
Session
→ Set Working Directory
→ Choose Directory
```

Sélectionner le dossier contenant les fichiers.

---

## Exécuter le Projet 1

Ouvrir :

```text
projet-1-racines-unitaires/Projet1_Baillet.R
```

Puis exécuter les sections dans l’ordre :

1. chargement des données ;
2. visualisation ;
3. décomposition ;
4. ACF et PACF ;
5. test DF ;
6. test ADF ;
7. test de Zivot-Andrews ;
8. test de Lee-Strazicich ;
9. différenciation ;
10. vérification de la stationnarité.

Le test de Lee-Strazicich nécessite également le fichier contenant la fonction :

```text
LeeStrazicichUnitRootTestParallelization.R
```

Ce fichier n’est pas présent dans le dépôt actuel et doit être ajouté pour reproduire cette partie intégralement.

---

## Exécuter le Projet 2

Ouvrir :

```text
projet-2-previsions-arima/Projet2_Baillet.R
```

Exécuter ensuite :

1. chargement des données ;
2. création de la série ;
3. différenciation ;
4. ACF et PACF ;
5. EACF ;
6. estimation des modèles ;
7. tests des coefficients ;
8. tests des résidus ;
9. calcul des BIC ;
10. prévisions.

---

# Compétences démontrées

## R

- importation de fichiers CSV ;
- filtrage de données ;
- traitement des valeurs manquantes ;
- création de séries temporelles ;
- graphiques ;
- fonctions statistiques ;
- programmation reproductible.

## Séries temporelles

- tendance ;
- saisonnalité ;
- hétéroscédasticité ;
- ACF ;
- PACF ;
- EACF ;
- différenciation ;
- stationnarité ;
- intégration d’ordre 1.

## Économétrie

- tests de racines unitaires ;
- choix des spécifications ;
- sélection des retards ;
- ruptures structurelles ;
- bootstrap ;
- processus DS ;
- modèles ARIMA ;
- tests de résidus.

## Prévision

- estimation de modèles ;
- comparaison par le BIC ;
- prévisions multi-horizons ;
- mise à jour du modèle ;
- comparaison entre prévisions et observations.

## Communication

- présentation de résultats statistiques ;
- explication pédagogique des tests ;
- interprétation économique ;
- structuration d’une étude en deux projets liés.

---

# Limites

- La série contient un nombre limité d’observations annuelles.
- Les résultats dépendent de la période étudiée.
- Certaines ruptures détectées sont interprétées économiquement sans démonstration causale.
- La série ne contient pas de variables explicatives externes.
- Les prix du pétrole ne sont pas intégrés directement au modèle.
- Les politiques énergétiques ne sont pas modélisées.
- Les événements climatiques ne sont pas intégrés comme variables explicatives.
- Le modèle ARIMA utilise uniquement l’historique de la série.
- Les prévisions deviennent plus incertaines lorsque l’horizon augmente.
- Le modèle sélectionné peut changer lorsqu’une nouvelle observation est ajoutée.
- Certaines fonctions utilisées pour Lee-Strazicich proviennent d’un script externe.
- Les résultats constituent un travail académique et non une prévision institutionnelle.

---

# Pistes d’amélioration

- automatiser la sélection des modèles ;
- utiliser une validation chronologique ;
- mesurer les erreurs MAE, RMSE et MAPE ;
- comparer les prévisions à `auto.arima` ;
- ajouter un modèle ETS ;
- ajouter un modèle de lissage exponentiel ;
- intégrer les prix mondiaux du pétrole ;
- intégrer le PIB thaïlandais ;
- intégrer la consommation énergétique ;
- intégrer les crises sous forme de variables indicatrices ;
- utiliser un modèle ARIMAX ;
- comparer avec Prophet ;
- tester des modèles à changements de régimes ;
- produire des prévisions avec plusieurs scénarios ;
- automatiser la mise à jour annuelle des données.

---

# Auteur

**Benjamin Baillet**

Master IREF-FQA  
Université de Bordeaux

Compétences principales :

- R ;
- RStudio ;
- séries temporelles ;
- économétrie ;
- ARIMA ;
- prévisions ;
- Python ;
- SQL ;
- Power BI.

GitHub : [benjaminblt](https://github.com/benjaminblt)
