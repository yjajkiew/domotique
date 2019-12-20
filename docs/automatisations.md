---
layout: default
title: Automatisations
nav_order: 4
comments: true
---

# Automatisations - Routines
{: .no_toc }

Cette section décris les automatisations principales que j'ai configuré dans Home Assistant. Je tâcherai de détailler les parties "velues" autant que possible.

Il est à noter que toutes les automatisations ci-dessous reposent sur la configuration de Home Assistant qui est décrite [ici](/hassio) (les capteurs, les capteurs custom, etc.)


1. TOC
{:toc}


## Notifications quand on arrive à la maison

*C'est la toute première automatisation que j'ai mis en place, elle n'apporte pas grand chose mais elle m'a permis de me familiariser avec Home Assistant !*

**Objectif :** recevoir une notification lorsque le conjoint arrive à la maison

**Pré-requis :**
 - [nmap](https://www.home-assistant.io/integrations/nmap_tracker/) et/ou [owntracks](https://www.home-assistant.io/integrations/owntracks/)
 - home assistant [accessible sur internet](/hassio.html#accès-depuis-lextérieur)
 - [notifications push](https://www.home-assistant.io/integrations/html5/)
 
 **Fonctionnement:**
 
Aux débuts pour tracker qui était présent à la maison j'ai utilisé l'intégration [netgear](https://www.home-assistant.io/integrations/netgear/) pour scanner les device sur le réseau domestique et possède l'avantage de gérer les Access Points vu que j'ai deux routeurs à la maison : le problème c'est que Home Assistant récupère bien les device mais la mise à jour ne s'effectuait pas lorsque l'on quittait le réseau.

Donc ensuite j'ai décidé d'utiliser l'intégration [nmap](https://www.home-assistant.io/integrations/nmap_tracker/) pour scanner le réseau régulièrement et savoir quels devices sont connectés ou non au réseau et ainsi savoir si moi et ma conjointe sommes à la maison. Cela a bien fonctionné un temps mais au bout d'un moment, et peu importe les réglages que j'ai pu effectuer, les moments de présence et d'absence n'étaient pas fiable et devenaient intermittent comme suivant :


<img src="assets/home-assistant-nmap.png" />


Ceci est sûrement dû aux smartphones qui coupent le wifi par intermittence pour économiser la batterie.

Au final pour être fiable je cumule :
 - nmap pour tracker les devices au global (routeurs, robot aspirateur, etc.) et pouvoir afficher cela dans le dashboard de [monitoring du réseau](/hassio.html#devices)
 - [owntracks](https://www.home-assistant.io/integrations/owntracks/) installé sur les smartphones pour envoyer les changements importants de position GPS à mon instance de home assistant
 
 Pour en savoir plus sur la détection de présence dans home assistant : [https://www.home-assistant.io/getting-started/presence-detection](https://www.home-assistant.io/getting-started/presence-detection/)
 
***Fichier automations.yaml***
```yaml
- id: '1569514492243'
  alias: Manon home notification
  trigger:
  - entity_id: person.manon
    from: not_home
    platform: state
    to: home
  condition: []
  action:
  - service: notify.firebase
    data:
      message: Manon est à la maison
      target: OnePlus5_Yann
      data:
        icon: "/local/manon.png"
  - service: system_log.write
    data_template:
      message: 'Manon home notification'
      level: info
    
    
- id: '1569577571785'
  alias: Yann home notification
  trigger:
  - entity_id: person.yann
    from: not_home
    platform: state
    to: home
  condition: []
  action:
  - service: notify.firebase
    data:
      message: Yann est à la maison
      target: OnePlus5t_Manon
      data:
        icon: "/local/yann.jpg"
  - service: system_log.write
    data_template:
      message: 'Yann home notification'
      level: info
```

**Astuces** :
 - il est possible de lier un device ou un profil owntracks sur une entité de type "person" pour pouvoir connaître l'état d'une personne et non d'un device
 - il est possible de notifier directement un device en précisant la target dans le service notify.firebase
 - il est possible d'ajouter des images dans les notifications, le chemin doit être relatif à l'URL publique de votre instance home assistant
 - j'ai mis en place des logs lorsqu'une notification est envoyée pour pouvoir le tracer
 - il est possible de définir des [zones](https://www.home-assistant.io/integrations/zone/) pour pouvoir avoir des états de présence supplémentaires à "home" tel que "travail" ou "famille" mais dans ce cas lorsque vous passez de l'état "travail" à l'état "home" cette automatisation ne fonctionnera pas car vous ne serez pas dans l'état "not_home"

Personnellement j'ai désactivé cette automatisation mais je la conserve en documentation car c'est un bon exemple pour débuter :-)

## Ouverture et fermeture des volets

**Objectif :** ouvrir et fermer automatiquement les volets de la maison en fonction de l'heure de levé et couché du soleil avec offset paramètrable

**Pré-requis :**
 - intégration de volets ([api somfy](https://www.home-assistant.io/integrations/somfy/) avec connexoon dans mon cas)
 - intégration du [soleil](https://www.home-assistant.io/integrations/sun/) pour connaître les heures de levé et couché chaque jour
 
 **Fonctionnement:**




## Allumer et éteindre le sèche serviette

**Objectif :** allumer automatiquement le sèche serviette si moi ou ma coinjointe sommes à la maison et que l'on est sur un jour travaillé

**Pré-requis :**
 - prise connectée connecté au sèche serviette (prise z-wave dans mon cas)
 - intégration [z-wave](https://www.home-assistant.io/integrations/zwave/)
 - [détection de présence](https://www.home-assistant.io/getting-started/presence-detection/)
 - intégration du binary sensor [workday](https://www.home-assistant.io/integrations/workday/) pour savoir si c'est un jour travaillé (du lundi au vendredi, jours feriés exclus pris en charge directement dans le binary sensor)
 
 **Fonctionnement:**
 
Tous les jours à 07h05 l'automatisation vérifie certaines conditions pour savoir s'il doit déclencher le sèche serviette pendant 30mn :
 - être présent à la maison (moi ou ma conjointe) : en mettant une condition sur la détection de présence cela permet de ne pas avoir à le désactiver manuellement si jamais on part en week-end ou en vacances
 - être sur un jour travaillé car cela évite de faire fonctionner le sèche serviette tôt le matin lorsque l'on travaille car le week-end on dort souvent plus tard et on ne sait pas à l'avance l'heure à laquelle on va se lever.


***Fichier automations.yaml***
```yaml
- id: '15706293690293'
  alias: Sèche serviette auto turn on
  trigger:
  - platform: time
    at: 07:05:00
  condition: # workday and (Manon or Yann is home)
    condition: and
    conditions:
    - condition: state
      entity_id: 'binary_sensor.workday_sensor'
      state: 'on'
    - condition: or
      conditions:
      - condition: state
        entity_id: person.yann
        state: home
      - condition: state
        entity_id: person.manon
        state: home
  action:
  - service: switch.turn_on
    data:
      entity_id: switch.fibaro_secheserviette_switch
  - service: system_log.write
    data_template:
      message: 'Turn on Sèche Serviette and wait 45mn'
      level: info
  - delay: "00:30:00"
  - service: switch.turn_off
    data:
      entity_id: switch.fibaro_secheserviette_switch
  - service: system_log.write
    data_template:
      message: 'Turn off Sèche Serviette after 45mn'
      level: info
```
 
 
## Lancement automatique de l'aspirateur

**Objectif :** lancer l'aspirateur un jour sur deux sauf si on est à la maison

**Pré-requis :**
 - aspirateur connecté (Xiaomi Mi Robot 1ère version dans mon cas)
 - intégration [xiaomi mi robot](https://www.home-assistant.io/integrations/vacuum.xiaomi_miio/)
 - intégration du binary sensor [workday](https://www.home-assistant.io/integrations/workday/) pour savoir si c'est un jour travaillé (du lundi au vendredi, jours feriés exclus pris en charge directement dans le binary sensor)
 - [détection de présence](https://www.home-assistant.io/getting-started/presence-detection/)
 
 **Fonctionnement:**

Tous les jours à 10h l'automatisation vérifie certaines conditions pour lancer l'aspirateur :
 - lorsque c'est le week-end ou un jour férié nous sommes présent à la maison ou nous sommes occupé à bricoler donc on ne souhaite que l'aspirateur se lance.
 - lorsque l'on est à la maison en cas de télétravail par exemple on ne souhaite pas que l'aspirateur se lance.
 - je préfère que l'aspirateur se lance tous les deux jours plutôt que tous les jours, cela permet d'économiser un peu d'énergie et d'économiser les consommables (filtres, brosses, etc).

Pour ce dernier point la technique est de calculer chaque matin si le dernier passage de l'aspirateur date de plus de 24h via un template :
```yaml
{{ ((as_timestamp(now())|int)-(as_timestamp(state_attr("vacuum.xiaomi_vacuum_cleaner", "clean_stop"))|int)|int)//(60 * 60) >= 24 }}
```

***Fichier automations.yaml***
```yaml
- id: '15706299592930'
  alias: Vacuum
  trigger:
  - platform: time
    at: "10:00:00"
  condition: 
    - condition: state
      entity_id: 'binary_sensor.workday_sensor'
      state: 'on'
    - condition: template
      value_template: "{{ not is_state('person.yann', 'home') }}"
    - condition: template
      value_template: "{{ not is_state('person.manon', 'home') }}"
    - condition: template
      value_template: '{{ ((as_timestamp(now())|int)-(as_timestamp(state_attr("vacuum.xiaomi_vacuum_cleaner", "clean_stop"))|int)|int)//(60 * 60) >= 24 }}'
  action:
  - service: vacuum.start
    data:
      entity_id: vacuum.xiaomi_vacuum_cleaner
  - service: system_log.write
    data_template:
      message: 'Launch vacuum cleaner'
      level: info
```
 
 
## Réveil lumineux

**Objectif :** recevoir une notification lorsque le conjoint arrive à la maison

**Pré-requis :**
 - toto
 
 **Fonctionnement:**


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

