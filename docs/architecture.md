---
layout: default
title: Architecture
nav_order: 2
comments: true
---

# Architecture
{: .no_toc }

1. TOC
{:toc}

## Principes

J'ai posé les principes suivants pour construire et maintenir ma solution domotique. S'ils ne sont pas stricts ils constituent des guidelines qui peuvent vous aider à comprendre les choix que j'ai pu faire :

 - **Utiliser les standards Web et IoT** : privilégier les standards W3C, HTML5, API REST, Z-Wave, ZigBee, MQTT etc.
 - **Privilégier l'existant au custom** : 
   - privilégier les addons, composants et intégrations de Home Assistant/Hass.io 
   - privilégier les devices du marché plutôt que de construire des capteurs maison *(e.g. capteur de température Z-Wave VS construction et programmation d'un capteur sous arduino)*
 - **Approche Best of breed** : incorporer du matériel et des technologies qui répondent à mes besoins plutôt que du "all in one"
 - **Fallback obligatoire** : étant donné qu'on est sur installation "maison" sans garantie de fonctionnement il me semble vital d'avoir des solutions de contournement pour que mes appareils puissent toujours fonctionner *(mes volets doivent toujours être utilisable même si Home Assistant tombe, mon sèche serviette doit toujours être allumable manuellement si Home Assistant tombe, etc.)*
 - **Ne pas installer d'éléments de sécurité "durs"** :
   - Je m'interdis : serrure connectée, alarme qui repose sur cette solution domotique, etc.
   - J'autorise les éléments de prévention : caméras, notifications et alertes, etc.
 
Ces principes me permettent d'assurer la cohérence de ma solution domotique au global, son bon fonctionnement, son évolutivité,  et son maintien *(je n'ai pas envie de passer mes soirées à débuguer ma maison connectée*).



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

{% include comments.md %}
