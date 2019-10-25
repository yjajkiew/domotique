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
 
Pour ma part je suis parti sur **Hassio** plutôt que sur l'installation standalone de Home Assistant. Normalement tout ce que je décris ci-dessous devrait être applicable à la fois pour Home Assistant pour Hassio, le dernier ayant l'avantage de faciliter certaines installations ou configurations.


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

**Avantages**: 
 - à la portée de tous
 - robuste
 
**Inconvénients**:
 - dépendant de la configuration de la box internet donc la configuration est à refaire en cas de changement de FAI
 - dépendant des limitations de la box internet ***(par exemple sur la LiveBox 3 de Orange l'accès par l'adresse IP internet public ne fonctionne pas à l'intérieur du réseau domestique)***

### Solution #2 : tunnel SSH

Cette solution permet de s'affranchir des limitations de la box internet et de mettre à profit l'utilisateur d'un serveur virtuel hébergé dans le cloud que vous possédez peut être déjà :

<img src="/assets/hassio-internet-3.PNG" />

Bien entendu pour communiquer avec le serveur le raspberry passe bien par internet et donc le routeur principal et la box internet, mais dans l'idée il s'affranchit des limites de NAT, firewall et autre puisque le tunnel SSH connecte directement le port de Home Assistant vers un port du serveur, qui peut alors être réutilisé comme source de reverse proxy pour l'exposer sur internet avec nginx et un certificat SSL.

**Avantages**: 
 - facilite la mise en place d'un nom de domaine et d'un certificat SSL
 - solution indépendante de la box internet (configuration et limitations)
 
**Inconvénients**:
 - nécessite d'avoir un serveur distant et d'avoir des compétences techniques plus fournies

**Astuces** :
 - il existe l'outil [autossh](https://linux.die.net/man/1/autossh) qui permet de créer des tunnels SSH persistent qui se reconstruisent en cas de fail
 - encore mieux il existe un [addon Hassio AutoSSH](https://github.com/pjcarly/hassio-addons) qu'il suffit d'installer et configurer pour que cela fasse partie intégrante de votre Hassio : [voir tutoriel](https://carly.be/expose-home-assistant-through-ssh-tunnel/)
 - désactiver la fonction monitoring de autossh et utiliser plutôt les options ServerAliveInterval et ServerAliveCountMax pour maintenir la connexion active entre home assistant et le serveur distant
 - activer le monitoring côté client (serveur distant) pour libérer le port en cas d'interruption de la connexion SSH (dans mon cas en cas de coupure de courant autossh ne pouvait pas couper correctement la connexion ainsi le port restait ouvert à tort sur le serveur distant, au redémarrage autossh ne pouvait donc plus se connecter au port du serveur distant puisqu'il restait bloqué) > [voir tutoriel](http://go2linux.garron.me/linux/2011/02/limit-idle-ssh-sessions-time-avoid-unattended-ones-clientaliveinterval-clientalivecoun/)
 - utiliser une adresse IP différente sur le serveur distant pour décorréler l'IP du reverse proxy de Home Assistant de vos sites publiques et éviter un discovery via un reverse IP lookup)
 - pour les configurations nginx et certificat SSL la documentation de Home Assistant fournit tout ce qu'il faut : [voir documentation](https://www.home-assistant.io/docs/ecosystem/nginx_subdomain/)
 - ma configuration autossh est la suivante
 
 ```
 {
  "hostname": "mondomainessh",
  "ssh_port": "22",
  "username": "homeassistant",
  "remote_forwarding": [
    "12300:localhosts:8123"
  ],
  "local_forwarding": [
    ""
  ],
  "other_ssh_options": "-fvnNT -o \"ServerAliveInterval 30\" -o \"ServerAliveCountMax 3\"",
  "monitor_port": "0"
}
 ```
 
*L'option -f permet de continuer à essayer de créer le tunnel SSH même en cas de fail, pratique en cas de coupure de courant et en reprise où home assistant a démarré mais la livebox n'a pas encore récupéré la synchronisation internet. Les options vnNT permettent d'interdire l'utilisation du tty.*


## Supervision

J'ai mis en place des éléments natifs (installation et configuration) mais également des configurations un peu plus custom pour superviser globalement Hassio.

Cette section détaille comment obtenir les données de supervision. Pour tout ce qui est visualisation rendez-vous sur la section dédiée [Dashboard Monitoring](#monitoring).

### Supervision des ressources physiques

Home Assistant fourni une intégration "[System Monitor](https://www.home-assistant.io/integrations/systemmonitor/)" qui permet de superviser les ressources physiques.
Pour cela il suffit d'ajouter la configuration suivante au niveau de l'attribut sensor du fichier configuration.yaml

```
sensor:
  # System monitor
  - platform: systemmonitor
    resources:
      - type: disk_use_percent
        arg: /
      - type: memory_use_percent
      - type: processor_use
      - type: network_in
        arg: eth0
      - type: network_out
        arg: eth0
      - type: throughput_network_in
        arg: eth0
      - type: throughput_network_out
        arg: eth0
      - type: last_boot
```

Il existe d'autres attributs mais ceux-ci me suffisent. 
*Il est à noter que plus vous ajouterez des éléments à superviser et plus votre  base de données sera lourde car toutes les valeurs sont historisées.*


### Glances

Avec Hassio vous pouvez installer en quelques clics le [Add-On Glances](https://github.com/hassio-addons/addon-glances) qui permet d'avoir une vue d'ensemble de son système : CPU, RAM, réseau, nombre de containers,...

<img src="https://raw.githubusercontent.com/hassio-addons/addon-glances/master/images/screenshot.png" />

A partir de Glances il est possible de compléter les sensors de supervision décris plus haut. Dans mon cas j'ai souhaité récupérer le nombre de containers en ajoutant la configuration suivante dans le fichier configuration.yaml :

```
sensor:
  - platform: glances
    host: !secret glances_host
    port: !secret glances_port
    version: 3
    resources:
      - 'docker_active'
```


### Configurations custom

En plus des sensors décris ci-dessus j'en ai ajouté quelques-uns natifs comme le uptime, la taille de la base de données, et la taille des logs via la configuration suivante :

```
sensor:
  # UpTime
  - platform: uptime
  # Filesize monitoring
  - platform: filesize
    file_paths:
      - /config/home-assistant_v2.db
      - /config/home-assistant.log
```

Je suis allé également un peu plus loin en utilisant les [templates](https://www.home-assistant.io/integrations/template/) de Home Assistant pour récupérer et calculer : le nombre de sensors, le nombre de lumières, le nombre de devices connus sur mon réseau, le nombre d'automatisations, le nombre de volets.

```
{% raw %}
sensor:
  # templates
  - platform: template
    sensors:
      sensors_count: # need to automate calling homeassistant.update_entity periodically
        friendly_name: "Sensors count"
        value_template: "{{ states.sensor|list|length }}"
        icon_template: mdi:eye
        entity_id: sensor.time # refresh this count every minute
      lights_count:
        friendly_name: "Lights count"
        value_template: "{{ states.light|list|length }}"
        icon_template: mdi:lightbulb
        entity_id: group.all_lights
      devices_count:
        friendly_name: "Devices count"
        value_template: "{{states.group.all_devices.attributes.entity_id|list|length}}"
        icon_template: mdi:robot-industrial
        entity_id: group.all_devices
      automations_count:
        friendly_name: "Automations count"
        value_template: "{{states.group.all_automations.attributes.entity_id|list|length}}"
        icon_template: mdi:robot
        entity_id: group.all_automations
      covers_count:
        friendly_name: "Covers count"
        value_template: "{{states.group.all_covers.attributes.entity_id|list|length}}"
        icon_template: mdi:window-closed
        entity_id: group.all_covers
{% endraw %}
```






## Backups

Une des premières choses que j'ai mis en place lorsque j'ai commencé à avoir une configuration stable c'est de mettre en place un système de backup déporté. Par chance il existe un addon [Hass.io Google Drive Backup](https://github.com/sabeechen/hassio-google-drive-backup) qui fait exactement ce que je souhaitais à savoir faire un backup complet (configurations home assistant, base de données, logs, network mesh de mon contrôleur Z-Wave,...) journalier et l'envoie sur mon Google Drive.

Son installation est simple via Hassio, ma configuration est la suivante :

```
{
  "max_snapshots_in_hassio": 10,
  "max_snapshots_in_google_drive": 10,
  "days_between_snapshots": 1,
  "use_ssl": false,
  "snapshot_time_of_day": "00:00"
}
```

*Remarque : cet addon contient également une option "snapshot_password" qui m'intéresse mais qui ne malheureusement ne fonctionne pas avec les secrets contrairement à ce qui est indiqué dans la documentation.*

Ainsi en cas de problèmes ou de réinstallation je peux restaurer un de ces backups directement via l'interface d'un Home Assistant tout frais.

## Configurations

### Général

Avant d'entrer dans les configurations je vous invite à utiliser les [secrets](https://www.home-assistant.io/docs/configuration/secrets/) : il suffit de créer un fichier secrets.yaml dans le dossier config pour y mettre tous les configurations sensibles tels que les IPs et mots de passe pour ensuite les inclure dans le fichier configuration.yaml

Configuration HTTP (absolument nécessaire lorsque Home Assistant est exposé derrière un reverse proxy nginx par exemple) :
```
http:
  # For extra security set this to only accept connections on localhost if NGINX is on the same machine
  # server_host: 127.0.0.1
  # Update this line to be your domain
  base_url: !secret base_url
  use_x_forwarded_for: true
  # You must set the trusted proxy IP address so that Home Assistant will properly accept connections
  # Set this to your NGINX machine IP, or localhost if hosted on the same machine.
  trusted_proxies: !secret http_trusted_proxies
```

Si dans la partie [supervision](#supervision) vous monitorez la taille de la BDD et des logs il faut autoriser la lecture de ces fichiers à travers la configuration suivante :
```
homeassistant:
  whitelist_external_dirs: 
    - '/config/'
```

Activer les logs :
```
# Activate logs
logger:
  default: info
```

Activer le sensor "updater" qui permet de prévenir lorsqu'une mise à jour est disponible :
```
# set hassio updater
updater:
  reporting: false
  include_used_components: false
```

Activer la gestion des entités "personnes" depuis l'interface de configuration de Home Assistant :
```
person: # enable persons manager from UI
```

### Add-ons et composants

**Configurator**

[Configurator](https://www.home-assistant.io/addons/configurator/) est un outil indispensable puisqu'il permet de gérer (créer, modifier, supprimer, renommer) ses fichiers de configuration directement depuis l'interface de Home Assistant. Un éditeur YAML avec vérification de syntaxe y est directement intégré.

**Log Viewer**

[LogViewer](https://github.com/hassio-addons/addon-log-viewer) est un outil qui permet de visualiser en temps réel les logs directement depuis l'interface de Home Assistant.

**SSH**

Cet [addon SSH](https://github.com/hassio-addons/addon-ssh) permet de configurer facilement SSH sous Hassio.

**AutoSSH**

Cette section est détaillée plus haut sur l'[exposition sur internet via tunnel SSH](#solution-2--tunnel-ssh)

**Breaking changes**

Ce composant n'est pas disponible en tant qu'Add-on Hassio installable en quelques clics, mais il est disponible via une [installation manuelle](https://developers.home-assistant.io/docs/en/creating_component_loading.html).

[Breaking changes](https://github.com/custom-components/breaking_changes) est un module qui va permettre de comparer votre configuration par rapport à la prochaine version de Home Assistant disponible et de prévenir du nombre de "potential breaking changes". Plutôt pratique pour anticiper les mises à jour sans avoir à scruter tout le changelog à chaque release.

Après installation le module est à activer en mettant ceci dans la fichier configuration.yaml :
```
# activate custom component "breaking changes"
breaking_changes:
```



### Sensors : capteurs et services



## Dashboards

### Thèmes et custom cards

### Accueil

### Devices

### Electricité

### Monitoring


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

### Notification quand il faut sortir les poubelles


{% include comments.md %}
