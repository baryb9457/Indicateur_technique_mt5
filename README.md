# Indicateur Technique MT5 — Dashboard Multi-Timeframe

Indicateur personnalisé pour **MetaTrader 5 (MT5)** affichant la tendance du marché sur plusieurs unités de temps simultanément.

![Aperçu de l'indicateur](https://github.com/user-attachments/assets/64ae07a2-e0fb-4713-b4d4-babe1dba6c6e)

## Description

Ce dashboard déplaçable affiche en temps réel la tendance (Hausse / Baisse / Range) sur 5 timeframes : M5, M30, H1, H4, D1.

L'analyse repose sur une Moyenne Mobile (MA) dont le type et la période sont configurables.

## Fonctionnalités

- Panneau graphique déplaçable (drag & drop)
- Analyse multi-timeframe (M5, M30, H1, H4, D1)
- Détection automatique de la tendance :
  - 📈 **HAUSSE** : prix au-dessus de la MA et MA montante
  - 📉 **BAISSE** : prix en dessous de la MA et MA descendante
  - ↔️ **RANGE** : faible pente de la MA
- Boutons cliquables pour changer rapidement de timeframe
- Couleurs et paramètres entièrement personnalisables

## Paramètres configurables

| Paramètre | Description | Défaut |
|---|---|---|
| `InpMAPeriod` | Période de la Moyenne Mobile | 21 |
| `InpMAMethod` | Type de MA (EMA, SMA…) | EMA |
| `InpRangePips` | Seuil Range en points | 15 |
| `ColorUp` | Couleur tendance haussière | Vert foncé |
| `ColorDown` | Couleur tendance baissière | Rouge foncé |
| `ColorRange` | Couleur Range | Marron |

## Installation

1. Copier le fichier `.mq5` dans le dossier `MQL5/Indicators/` de MetaTrader 5
2. Compiler depuis l'éditeur MetaEditor (F7)
3. Appliquer l'indicateur sur un graphique

## Auteur

KAM — version 2.00

<img width="837" height="456" alt="image d&#39;indicateur" src="https://github.com/user-attachments/assets/64ae07a2-e0fb-4713-b4d4-babe1dba6c6e" />



