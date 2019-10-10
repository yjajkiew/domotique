---
layout: default
title: Hassio
nav_order: 3
---

# Hassio - Home Assistant
{: .no_toc }

Avant de commencer je préfère mettre en avant un élément qui me semble important à préciser dans le vocabulaire utilisé sur cette page : **Hassio et Home Assistant ne sont pas exactement les mêmes choses** !
 - **Home Assistant** : c'est le programme python qui peut être installé n'importe où et qui permet de superviser, contrôler et automatiser ses devices
 - **Hassio** : c'est un ensemble d'outils en plus de Home Assistant qui permettent de faciliter l'installation et la maintenance. Il permet entre autre
   - d'avoir une interface graphique pour la gestion du serveur
   - d'accéder à l'installation et la gestion d'add-ons par interface graphique, alors que sur Home Assistant en standalone il faudrait ajouter ces modules complémentaires manuellement dans des containers Docker différents alors que sous Hassio c'est totalement transparent
   - de faciliter la restauration de backups complets
 
Pour ma part je suis parti sur Hassio plutôt que sur l'installation standalone de Home Assistant. Normalement tout ce que je décris ci-dessous devrait être applicable à la fois pour Home Assistant pour Hassio, le dernier ayant l'avantage de faciliter certaines installations ou configurations.


1. TOC
{:toc}

## Installation

Si vous êtes passé par les pages [matériel](/materiel) ou [architecture](/architecture) vous aurez compris que j'ai installé Hassio sur mon Raspberry Pi. A terme je souhaite passer sur du matériel plus robuste avec une installation sur un mini PC fanless car sur raspberry les nombreux accès en écriture sur la carte SD ont tendance à la flinguer... Mais pour le moment ça fait très bien l'affaire puisque ça permet de réaliser mes installations et configurations, sachant que niveau performances un RPI tient très bien la route pour le moment.

L'installation de Hassio sur RPI est ultra simple puisqu'il suffit de flasher la carte SD, connecter le raspberry en ethernet à votre réseau et allumer le tout : il est important qu'au démarrage le RPI soit connecté à internet puisqu'au démarrage l'image télécharger toutes les dépendances ainsi que la dernière version de Home Assistant, donc l'installation peut prendre 20 à 30mn.

La documentation officielle en fonction de votre matériel est disponible ici : https://www.home-assistant.io/hassio/installation/


## Accès depuis l'extérieur



## Supervision


## Configurations


## Dashboards


## Automations

