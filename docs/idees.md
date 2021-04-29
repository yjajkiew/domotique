---
layout: default
title: Idées
nav_order: 11
comments: true
---

# TODO LIST Documentation

- mettre à jour le matériel (capteurs de température, aqara open sensors, ampoules philips hue color, ampoules innr color, sirène aeotec, bandeau led color lidl, prise connectée lidl)
- intégration enedis : https://www.canaletto.fr/post/home-assistant-and-enedis
- cartes 3D avec affichage des sensors



# Idées d'automatisations à venir
{: .no_toc }

1. TOC
{:toc}

## Onduleur et rétablissement d'état en coupure de courant

https://www.canaletto.fr/post/home-assistant-restauration-etat-apres-coupure-electrique

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


## Allumer/Eteindre la chaudière fioul en fonction du gel 

La chaudière fioul fonctionne en renfort lors des jours de gel, actuellement je la branche ou débranche en fonction des prévisions même si le "cerveau" fait la bascule automatiquement. Cela permet de ne pas consommer du fioul pendant le mode hors gel lorsqu'elle n'est pas utilisée.

L'idée ici serait d'automatiser ce ON/OFF en fonction des prévisions météo. L'avantage serait également de pouvoir suivre l'utilisation de la chaudière pour voir quand elle se déclenche exactement.

**Matériel requis** :
 - Fibaro wall plug (prise z-wave avec suivi de consommation électrique)
 





{% include comments.md %}
