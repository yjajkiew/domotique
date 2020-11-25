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


## Dashboards

### Accueil

<a href="/assets/dashboard-home.jpg" target="_blank"><img src="/assets/dashboard-home.jpg" /></a>

### Devices

<a href="/assets/dashboard-devices.jpg" target="_blank"><img src="/assets/dashboard-devices.jpg" /></a>

### Electricité

<a href="/assets/dashboard-electricity.jpg" target="_blank"><img src="/assets/dashboard-electricity.jpg" /></a>

### Monitoring

<a href="/assets/dashboard-monitoring.jpg" target="_blank"><img src="/assets/dashboard-monitoring.jpg" /></a>

## Pourquoi choisir Home Assistant ?

Lorsqu'il s'agit de domotiser son domicile le choix de l'orchestrateur est crucial puisqu'il est central : il doit pouvoir recueillir des informations venant de divers systèmes, également en commander certains, et bien entendu pouvoir fournir une interface d'utilisation simple et efficace pour restituer tout cela.

Pour ma part je ne souhaitais absolument pas partir vers une solution propriétaire, quelle soit logicielle ou matérielle *(type box domotique toute prête)* pour faciliter son évolution, sa scalabilité, et surtout rester maître de sa maîtrise.

Quand on regarde l'écosystème des solutions domotiques logicielles globalement les plus courantes sont Jeedom, Domotics, Jeedom, OpenHab et Home Assistant.

Les éléments qui m'ont vraiment séduis chez Home Assistant sont les suivants :
 - open source
 - communauté active
 - se connecte avec un grand nombre de systèmes différents nativement (pour mes besoins actuels et besoins futurs)
 - une documentation extrêmement fournie et claire (ça c'est un point très important)
 - la configuration est simple via YML
 - l'interface graphique est séduisante
 - le support natif des PWA et des push notifications

## Installation

Si vous êtes passé par les pages [matériel](/materiel) ou [architecture](/architecture) vous aurez compris que j'ai installé Hassio sur mon Raspberry Pi. A terme je souhaite passer sur du matériel plus robuste avec une installation sur un mini PC fanless car sur raspberry les nombreux accès en écriture sur la carte SD ont tendance à la flinguer... Mais pour le moment ça fait très bien l'affaire puisque ça permet de réaliser mes installations et configurations, sachant que niveau performances un RPI tient très bien la route pour le moment.

L'installation de Hassio sur RPI est ultra simple puisqu'il suffit de flasher la carte SD, connecter le raspberry en ethernet à votre réseau et allumer le tout : il est important qu'au démarrage le RPI soit connecté à internet puisqu'au démarrage l'image télécharger toutes les dépendances ainsi que la dernière version de Home Assistant, donc l'installation peut prendre 20 à 30mn.

Update 02-2020 : à la place d'une carte SD qui peut se corrompre plus facilement j'utilise désormais une clé USB bootable
 1. Flasher une carte SD avec une raspbian Lite, activer le ssh, s'y connecter et activer le paramètre de boot usb : [documentation](https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/msd.md) et [tutoriel](https://www.instructables.com/id/Booting-Raspberry-Pi-3-B-With-a-USB-Drive/) > cette étape n'est pas nécessaire sur certains modèles, vérifiez la documentation avant !
 2. Flasher l'image Hassio sur la carte USB, la brancher sur le RPi, retirer la carte SD, brancher l'alimentation et le tour est joué !

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
 - activer le monitoring côté client (serveur distant) pour libérer le port en cas d'interruption de la connexion SSH (dans mon cas en cas de coupure de courant autossh ne pouvait pas couper correctement la connexion ainsi le port restait ouvert à tort sur le serveur distant, au redémarrage autossh ne pouvait donc plus se connecter au port du serveur distant puisqu'il restait bloqué) > [voir tutoriel](https://www.techrepublic.com/article/how-to-prevent-unattended-ssh-connections-from-remaining-connected/)
 - utiliser une adresse IP différente sur le serveur distant pour décorréler l'IP du reverse proxy de Home Assistant de vos sites publiques et éviter un discovery via un reverse IP lookup
 - pour les configurations nginx et certificat SSL la documentation de Home Assistant fournit tout ce qu'il faut : [voir documentation](https://www.home-assistant.io/docs/ecosystem/nginx_subdomain/)
 - ma configuration autossh est la suivante
 
 ```json
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

```yaml
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

```yaml
glances:
  - host: !secret glances_host
  - port: !secret glances_port
```


### Configurations custom

En plus des sensors décris ci-dessus j'en ai ajouté quelques-uns natifs comme le uptime, la taille des logs mais aussi la taille de ma base MariaDB via la configuration suivante :

```yaml
sensor:
  # UpTime
  - platform: uptime
  # Filesize monitoring
  - platform: filesize
    file_paths:
      - /config/home-assistant.log
  # database monitoring
  - platform: sql
    db_url: !secret db_url
    queries:
      - name: MariaDB Size
        query: 'SELECT table_schema "database", Round(Sum(data_length + index_length) / 1048576, 2) "value" FROM information_schema.tables WHERE table_schema="homeassistant" GROUP BY table_schema;'
        column: 'value'
        unit_of_measurement: MB
```

Je suis allé également un peu plus loin en utilisant les [templates](https://www.home-assistant.io/integrations/template/) de Home Assistant pour récupérer et calculer : le nombre de sensors, le nombre de lumières, le nombre de devices connus sur mon réseau, le nombre d'automatisations, le nombre de volets.

```yaml
{% raw %}
sensor:
  # templates
  - platform: template
    sensors:
      sensors_count: 
        friendly_name: "Sensors count"
        value_template: "{{states.sensor|list|length}}"
        icon_template: mdi:eye
      binarysensors_count: 
        friendly_name: "Binary Sensors count"
        value_template: "{{states.binary_sensor|list|length}}"
        icon_template: mdi:circle-slice-8
      lights_count:
        friendly_name: "Lights count"
        value_template: "{{states.light|list|length}}"
        icon_template: mdi:lightbulb
      devices_count:
        friendly_name: "Devices tracker count"
        value_template: "{{states.device_tracker|list|length}}"
        icon_template: mdi:robot-industrial
      automations_count:
        friendly_name: "Automations count"
        value_template: "{{states.automation|list|length}}"
        icon_template: mdi:robot
      covers_count:
        friendly_name: "Covers count"
        value_template: "{{states.cover|list|length}}"
        icon_template: mdi:window-closed
      groups_count:
        friendly_name: "Groups count"
        value_template: "{{states.group|list|length}}"
        icon_template: mdi:account-group
      inputs_count:
        friendly_name: "Inputs count"
        value_template: "{{states.input_boolean|list|length + states.input_number|list|length + states.input_datetime|list|length}}"
        icon_template: mdi:import
      scripts_count:
        friendly_name: "Scripts count"
        value_template: "{{states.script|list|length}}"
        icon_template: mdi:script
      switch_count:
        friendly_name: "Switch count"
        value_template: "{{states.switch|list|length}}"
        icon_template: mdi:toggle-switch

{% endraw %}
```

### Supervision des routeurs

Cela ne concerne pas la supervision de Home Assistant en soit mais plutôt l'inverse : la supervision de mes routeurs et de ma LiveBox depuis Home Assistant pour tout regrouper à un seul endroit.

Dans mon cas mon FAI est Orange via une Livebox donc j'ai simplement installé le plugin custom [Orange Livebox](https://github.com/cyr-ius/hass-livebox-component) qui me permet de vérifier le status de ma connexion, mon adresse WAN, et d'avoir un bouton de reboot.

Mon routeur principal par lequel passe tout mon traffic internet est un routeur Netgear R7000 et j'ai installé le plugin custom [Netgear Enhanced](https://gitlab.landry.me/Home-Assistant/custom_components/netgear-enhanced-sensor) qui permet de connaître le nombre total de Go downloaded/uploaded dans le mois courant et dans le mois précédent.


## Backups

Une des premières choses que j'ai mis en place lorsque j'ai commencé à avoir une configuration stable c'est de mettre en place un système de backup déporté. Par chance il existe un addon [Hass.io Google Drive Backup](https://github.com/sabeechen/hassio-google-drive-backup) qui fait exactement ce que je souhaitais à savoir faire un backup complet (configurations home assistant, base de données, logs, network mesh de mon contrôleur Z-Wave,...) journalier et l'envoie sur mon Google Drive.

Son installation est simple via Hassio, ma configuration est la suivante :

```json
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

Configuration générale de Home Assistant permettant de définir les URLs internes et externes à utiliser et d'inclure des fichiers de configurations splittés :
```yaml
homeassistant:
  external_url: !secret external_url
  internal_url: !secret internal_url
  whitelist_external_dirs: 
    - '/config/'
  customize: !include customize.yaml
  packages:
    inputs: !include inputs.yaml
    integrations: !include integrations.yaml
    system: !include system.yaml
```

Si dans la partie [supervision](#supervision) vous monitorez la taille de la BDD et des logs il faut autoriser la lecture de ces fichiers à travers la configuration suivante :
```yaml
homeassistant:
  whitelist_external_dirs: 
    - '/config/'
```

Activer les logs :
```yaml
# Activate logs
logger:
  default: info
```

Activer le sensor "updater" qui permet de prévenir lorsqu'une mise à jour est disponible :
```yaml
# set hassio updater
updater:
  reporting: false
  include_used_components: false
```

Activer la gestion des entités "personnes" depuis l'interface de configuration de Home Assistant :
```yaml
person: # enable persons manager from UI
```

Désactiver le discovery automatique de Google Cast (à chaque démarrage Home Assistant découvre le cast de ma box android tv et émet une notification qui ne m'intéresse pas) :
```yaml
discovery:
  ignore:
    - google_cast # ignore google cast from my android tv
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
```yaml
# activate custom component "breaking changes"
breaking_changes:
```



### Sensors : capteurs et services

- [z-wave](https://www.home-assistant.io/integrations/zwave/) : permet de gérer le contrôleur z-wave connecté au RPI3
- [xiaomi mi robot](https://www.home-assistant.io/integrations/vacuum.xiaomi_miio/) : permet de contrôler l'aspirateur robot
- [somfy api](https://www.home-assistant.io/integrations/somfy/) : permet de contrôler mes volets (auparavant j'utilisais l'intégration [tahoma](https://www.home-assistant.io/integrations/tahoma/) qui fonctionne avec connexoon mais les APIs non officielles ont été dépréciées)
- [linky](https://www.home-assistant.io/integrations/linky/) : permet de superviser ma consommation électrique
- [speedtest](https://www.home-assistant.io/integrations/speedtestdotnet/) : permet de superviser l'état de ma connexion internet
- [workday](https://www.home-assistant.io/integrations/workday/) : permet de connaître si le jour courant est un jour travaillé (avec prise en compte des jours fériés)
- [google](https://www.home-assistant.io/integrations/google_assistant/) : pour importer mon calendrier des poubelles et recevoir des notifications quand je dois les sortir
- [transmission](https://www.home-assistant.io/integrations/transmission/) : permet de contrôler et superviser mon client torrent



### Thèmes et custom cards

TODO



{% include comments.md %}
