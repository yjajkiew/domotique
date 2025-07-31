---
layout: default
title: Troubleshooting
nav_order: 5
comments: true
---

# Troubleshooting - Dépannage
{: .no_toc }

1. TOC
{:toc}


## Home Assistant ne se connecte plus à Google Calendar

Sans raison apparente Home Assistant n'arrivait plus à se connecter à calendrier Google.
J'ai trouvé la solution sur github : [https://github.com/home-assistant/home-assistant/issues/27210#issuecomment-538891558](https://github.com/home-assistant/home-assistant/issues/27210#issuecomment-538891558)

1. supprimer le fichier /config/.google.token
2. commenter l'intégration google dans votre fichier configuration.yaml
3. Redémarrer Home Assistant
4. remettre l'intégration google en décommentant les lignes de votre configuration.yaml
5. Redémarrer Home Assistant
6. Dans les notifications il vous sera demandé de vous reconnecter Home Assistant à votre compte Google pour autoriser l'accès
7. Voilà !

## Hassio Google Drive Backup ne se lance plus

Après une mise à jour de ce plugin en version 0.100 il ne lançait plus, avec un message d'erreur associé qui était le suivant :

```
ERROR (SyncWorker_17) [hassio.docker] Can't create container from addon_cebe7a76_hassio_google_drive_backup: 500 Server Error: Internal Server Error ("error creating overlay mount to /mnt/data/docker/overlay2/061e276c35ebac31eb02d9852f6cddf3106b30fb727af89f87fcc9a59919d3e9-init/merged: bad message")
```

La solution, un peu radicale, fut de désinstaller le plugin et de le réinstaller.
Pensez-bien à remettre votre configuration du plugin et d'y aller faire un tour via l'UI pour vous re-connecter à Google Drive !

## The Recorder could not start, please check the log

Après un redémarrage de Home Assistant cette erreur peut survenir en utilisant l'add-on MariaDB (qui remplace la base SQLite par défaut).
Home Assistant fonctionne correctement mais il n'enregistre pas les changements d'état en base donc il n'y a plus d'historique.

La seule solution qui fonctionne chez moi est de désinstaller le plugin, de le réinstaller puis de redémarrer Home Assistant.

## System is not ready with state setup

Souvent cela m'arrive après un redémarrage forcé, type coupure de courant, de la VM qui héberge Home Assistant sur mon NAS.
 - Si au démarrage de la VM celle-ci semble vraiment pas lancer home assistant après quelques minutes et vous invite à un prompt vide, lancez juste la commande : 
```
login
```
 - Lorsque le message "System is not ready with state setup" semble être en boucle juste demande à home assistant de se lancer peut suffire avec la commande :
```
banner
```
 - Dans le dernier cas lorsque le prompt **ha >** est présent on peut alors lancer ces commandes :
```
supervisor repair
supervisor restart
banner
```
   

