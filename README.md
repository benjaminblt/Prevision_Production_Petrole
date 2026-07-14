# Analyse et prévision de la production pétrolière

Diagnostiquer la stationnarité d’une série annuelle, détecter ses ruptures structurelles et produire des prévisions ARIMA actualisables.

## Vue d’ensemble

Ce projet regroupe deux études complémentaires sous R sur la production annuelle de pétrole en Thaïlande :

1. analyse de stationnarité et détection de ruptures ;
2. sélection d’un modèle ARIMA et prévision 2022-2025.

La série couvre la période 1971-2021, puis est actualisée avec la valeur observée de 2022.

## Projet 1 - Diagnostic statistique

La série en niveau présente :

- une tendance croissante ;
- une variance instable ;
- une forte dépendance temporelle ;
- plusieurs ruptures autour de 2000, 2011 et 2016.

Les méthodes utilisées comprennent :

- ACF et PACF ;
- Dickey-Fuller et Dickey-Fuller augmenté ;
- Zivot-Andrews ;
- Lee-Strazicich avec bootstrap ;
- différenciation d’ordre 1.

Les tests convergent vers un processus non stationnaire de type difference-stationary. La série est donc différenciée une fois avant la modélisation.

## Projet 2 - Prévision

Dix-huit spécifications ARIMA sont comparées. Le choix final repose sur :

- la significativité des coefficients ;
- le diagnostic des résidus ;
- la parcimonie ;
- le BIC.

Le modèle initial retenu est **ARIMA(3,1,2)**.

| Année | Prévision |
|---|---:|
| 2022 | 8 786,7 |
| 2023 | 7 873,1 |
| 2024 | 7 127,3 |
| 2025 | 7 025,1 |

Valeurs en milliers de tonnes d’équivalent pétrole.

Après ajout de la valeur observée de 2022, le meilleur modèle devient **ARIMA(3,1,0)**.

| Année | Prévision actualisée |
|---|---:|
| 2023 | 7 861,2 |
| 2024 | 7 109,4 |
| 2025 | 6 865,9 |

Cette évolution montre qu’un modèle de séries temporelles doit être réestimé lorsque de nouvelles données deviennent disponibles.

## Technologies

`R` · séries temporelles · tests de racine unitaire · ruptures structurelles · bootstrap · ARIMA · BIC · diagnostic des résidus

## Ce que ce projet démontre

- validation statistique avant prévision ;
- sélection rigoureuse de modèles ;
- interprétation de ruptures ;
- contrôle des résidus ;
- production de prévisions actualisables.

## Limites et améliorations

La série est annuelle et relativement courte. Une suite pertinente serait d’ajouter une validation glissante, des variables explicatives macroéconomiques et une comparaison avec ETS ou ARIMAX.

## Auteur

Projet réalisé par **Benjamin BAILLET** dans le cadre du Master IREF - Finance quantitative & Actuariat.

---
