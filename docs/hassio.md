---
layout: default
title: Hassio - Home Assistant
nav_order: 3
comments: true
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

La documentation officielle en fonction de votre matériel est disponible ici : [https://www.home-assistant.io/hassio/installation/](https://www.home-assistant.io/hassio/installation/)


## Accès depuis l'extérieur

Il est vital que je puisse accéder à Home Assistant en dehors de mon réseau domestique. 

Pour cela il y a deux solutions possibles que je détaille ci-dessous et que j'ai pu tester. Pour ma part j'ai préféré conserver la [solution #2](#solution-2--tunnel-ssh) pour m'affranchir des limitations de la box internet et d'avoir quelque chose de pérenne même en cas de changement de FAI. 

### Solution #1 : déclenchement de ports par la box internet

Cette solution est la plus simple à mettre en place puisqu'il suffit de configurer sa box internet (et autres routeurs derrière) pour rediriger les ports d'entrée vers le périphérique qui héberge Home Assistant :

<img src="/assets/hassio-internet-1.PNG" />

Le problème de cette solution avec une Livebox c'est que lorsque vous êtes dans votre réseau domestique, donc sur votre smartphone en wifi chez vous, vous ne pouvez pas accéder à l'IP publique de votre livebox : les accès sont refusés. Ceci oblige alors, si vous souhaitez conserver la même URL que vous soyez à l'extérieur ou à l'intérieur, de passer par un tier de type serveur virtual hébergé dans le cloud :

<img src="/assets/hassio-internet-2.PNG" />

 - **avantages**: 
   - à la portée de tous
   - robuste
 - **inconvénients**:
   - dépendant de la configuration de la box internet donc la configuration est à refaire en cas de changement de FAI
   - dépendant des limitations de la box internet ***(par exemple sur la LiveBox 3 de Orange l'accès par l'adresse IP internet public ne fonctionne pas à l'intérieur du réseau domestique)***

### Solution #2 : tunnel SSH

Cette solution permet de s'affranchir des limitations de la box internet et de mettre à profit l'utilisateur d'un serveur virtuel hébergé dans le cloud que vous possédez peut être déjà :

<img src="/assets/hassio-internet-2.PNG" />

Bien entendu pour communiquer avec le serveur le raspberry passe bien par internet et donc le routeur principal et la box internet, mais dans l'idée il s'affranchit des limites de NAT, firewall et autre puisque le tunnel SSH connecte directement le port de Home Assistant vers un port du serveur, qui peut alors être réutilisé comme source de reverse proxy pour l'exposer sur internet avec nginx et un certificat SSL.

 - **avantages**: 
   - facilite la mise en place d'un nom de domaine et d'un certificat SSL
   - solution indépendante de la box internet (configuration et limitations)
 - **inconvénients**:
   - nécessite d'avoir un serveur distant et d'avoir des compétences techniques plus fournies

**Astuces** :
 - désactiver la fonction monitoring de autossh et utiliser plutôt les options ServerAliveInterval et ServerAliveCountMax 
 - activer le monitoring côté client (serveur distant) pour libérer le port en cas d'interruption de la connexion SSH (dans mon cas en cas de coupure de courant autossh ne pouvait pas couper correctement la connexion ainsi le port restait alors ouvert et utilisé sur le serveur distant, au redémarrage autossh ne pouvait donc plus se connecter au port du serveur distant puisqu'il restait bloqué) > http://go2linux.garron.me/linux/2011/02/limit-idle-ssh-sessions-time-avoid-unattended-ones-clientaliveinterval-clientalivecoun/
 - utiliser une adresse IP différente sur le serveur distant pour décorréler l'IP du reverse proxy de Home Assistant de vos sites publiques et éviter un discovery via un reverse IP lookup)


## Supervision



## Backups



## Configurations


## Dashboards


## Automations

### Notifications quand on arrive à la maison

### Ouverture et fermeture des volets

### Allumer et éteindre le sèche serviette

### Réveil lumineux



***Fichier configuration.yaml***
```yaml
input_boolean:
  wakeup_weekday:
    name: 'Enable wakeup light'
    icon: mdi:power
    initial: off

input_datetime:
  wakeup_weekday_time:
    name: Heure
    has_date: false
    has_time: true
```


***Fichier groups.yaml***
```yaml
wakeup_week_day_panel:
  name: Alarme de semaine
  entities:
    - input_boolean.wakeup_weekday
    - input_datetime.wakeup_weekday_time
```


***Fichier automations.yaml***
```yaml
- id: '1570629369029'
  alias: Bedroom wake-up light
  trigger:
  - platform: time_pattern
    minutes: '/1'
    seconds: 0
  condition:
  - condition: state
    entity_id: person.yann
    state: home
  - condition: time
    weekday:
    - mon
    - tue
    - wed
    - thu
    - fri
  - condition: state
    entity_id: input_boolean.wakeup_weekday
    state: 'on'
  - condition: template
    value_template: '{{ ((as_timestamp(now())|int)|timestamp_custom("%H:%M:00")) == states("input_datetime.wakeup_weekday_time") }}'
  action:
  - service: light.turn_on
    entity_id: light.chambre
    data:
      brightness: 1
  - delay: 00:00:10
  - service: light.turn_on
    data:
      entity_id: light.chambre
      brightness: 100
      transition: 600
    
- id: '1570629959290'
  alias: Bedroom light auto turn off
  trigger:
  - hours: '08'
    minutes: '00'
    platform: time_pattern
    seconds: '00'
  condition:
  - condition: state
    entity_id: person.yann
    state: home
  - condition: time
    weekday:
    - mon
    - tue
    - wed
    - thu
    - fri
  action:
  - data:
      entity_id: light.chambre
    service: light.turn_off
```

### Notification quand il faut sortir les poubelles


{% include comments.md %}
