---
layout: default
title: Automatisations
nav_order: 4
comments: true
---

# Automatisations - Routines
{: .no_toc }

Avant de commencer je préfère mettre en avant un élément qui me semble important à préciser dans le vocabulaire utilisé sur cette page : **Hassio et Home Assistant ne sont pas exactement les mêmes choses** !
 - **Home Assistant** : c'est le programme python qui peut être installé n'importe où et qui permet de superviser, contrôler et automatiser ses devices
 - **Hassio** : c'est un ensemble d'outils en plus de Home Assistant qui permettent de faciliter l'installation et la maintenance. Il permet entre autre
   - d'avoir une interface graphique pour la gestion du serveur
   - d'accéder à l'installation et la gestion d'add-ons par interface graphique, alors que sur Home Assistant en standalone il faudrait ajouter ces modules complémentaires manuellement dans des containers Docker différents alors que sous Hassio c'est totalement transparent
   - de faciliter la restauration de backups complets
 
Pour ma part je suis parti sur **Hassio** plutôt que sur l'installation standalone de Home Assistant. Normalement tout ce que je décris ci-dessous devrait être applicable à la fois pour Home Assistant pour Hassio, le dernier ayant l'avantage de faciliter certaines installations ou configurations.


1. TOC
{:toc}

TODO
