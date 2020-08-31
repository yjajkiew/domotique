---
layout: default
title: Idées
nav_order: 11
comments: true
---

# Idées d'automatisations à venir
{: .no_toc }

1. TOC
{:toc}

## Détecter une livraison de courrier ou de colis dans la boîte aux lettres

Deux options :
- utiliser un détecteur d'ouverture de porte à mettre dans la boîte aux lettres :
  - avantage : permet de savoir si un colis a été livré
  - inconvénient : ne permet pas de savoir si une lettre à été livrée
- utiliser un détecteur de vibration/choc Aqara à mettre dans la boîte aux lettres : 
  - avantage : permet de savoir si un colis ou une lettre a été livré
  - inconvénient : se déclenchera lorsque l'on ouvrira la boîte aux lettres de l'intérieur pour récupérer le courrier

**Matériel requis** :
 - Aqara Vibration Shock sensor
 - Aqara Door sensor

## Fermer les volets s'il fait trop chaud

"Y'a plus qu'à" comme on dit : les capteurs de température sont présents (Xiaomi Aqara) et les volets du RDC sont déjà domotisés (Somfy Connexoon) donc il n'y a plus qu'à créer l'automatisation mais également aux cas tordus (feu de cheminée qui pourrait déclencher l'action alors que ce n'est pas voulu ou autre).

## Notification lorsque quelqu'un sonne à la porte

L'objectif serait d'être prévenu lorsqu'on sonne à la porte puisqu'on entend pas le carillon lorsque l'on est sur la terrasse par exemple.
Utiliser un module Z-Wave relay switch type Fibaro FGS213 connecté au carillon pour détecter le déclenchement de la sonnette et envoyer une notification. 

**Matériel requis** :
 - Fibaro FGS 213 : https://www.domotique-store.fr/domotique/modules-domotiques/micromodules/micromodules-switch-domotique-sans-fil/667-fibaro-fgs-213-single-switch-2-micromodule-interrupteur-simple-on-off-z-wave-avec-mesure-de-consommation.html

## Bandeaux leds au dessus des fenêtres synchronisés avec la TV Ambilight

It's a stretch puisque pour l'instant je possède certes :
 - une TV Philips Ambilight mais de 2014 donc pas synchronisable avec Philips Hue
 - des néons au dessus des coffrages de volet du salon

**Matériel requis** :
 - Une nouvelle TV Ambilight plus récente
 - des led strips de couleur avec contrôleur ZigBee de marque Philips (impératif pour que ce soit fonctionnel avec Philips Hue Entertainment)
 
Un petit peu d'électricité pour remplacer les néons et ensuite c'est Philips Hue qui fera le reste :-)






{% include comments.md %}
