---
layout: default
title: Architecture
nav_order: 2
---

# Architecture
{: .no_toc }

1. TOC
{:toc}

## Vision macro

Le schéma suivant décrit succintement le [matériel](/materiel) et les interconnexions pour assurer ma domotique :

<a href="assets/domotique_architecture_logique.jpg" target="_blank"><img src="assets/domotique_architecture_logique.jpg" /></a>


- Home Assistant est installé sur Raspberry Pi *(à terme il devra être installé sur un mini PC Fanless plus robuste)*
- Un dongle Z-wave est branché en USB directement dessus, il permet de contrôler mes prises Fibaro :
  - une pour allumer automatiquement le sèche serviette par exemple du lundi au vendredi à 6h30 et l'éteindre à 8h30, seulement si nous sommes à la maison
  - une pour superviser la consommation d'énergie du lave-linge, permettant d'envoyer une notification lorsque le lave-linge a fini son cycle
- Home Assistant s'interface avec divers périphériques sur le réseau domestique, qui contrôlent eux-même certains éléments :
  - scan du réseau sur les routeurs Wifi pour vérifier de la présence de ma conjointe, de ma belle-famille et de moi-même
  - Philips Hue pour le contrôle des lumières et certaines prises électriques
  - Connexoon pour le contrôle des volets de la porte de garage
  - Le robot aspirateur Xiaomi V1
- Home Assistant communique avec des services externes :
  - enedis pour la récupération de mes données de consommation électrique via le compteur Linky
  - SpeedTest pour réaliser des tests de connexion internet toutes les 6h et pouvoir superviser sa stabilité
  - Firebase pour pouvoir envoyer des notifications push sur les téléphones respectifs de ma conjointe et moi-même


## Architecture physique et infrastructure réseau

*Les éléments en opacité réduite sont des éléments à venir qui ne sont pas encore achetés, installés ou automatisés. Cela me permet de mettre en perspective la roadmap des prochains sujets.*

<a href="assets/domotique_architecture_physique.jpg" target="_blank"><img src="assets/domotique_architecture_physique.jpg" /></a>
