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


## Notifications quand on arrive à la maison

## Ouverture et fermeture des volets

## Allumer et éteindre le sèche serviette

## Réveil lumineux



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
{% raw %}
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
{% endraw %}
```

## Notification quand il faut sortir les poubelles

