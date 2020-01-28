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
