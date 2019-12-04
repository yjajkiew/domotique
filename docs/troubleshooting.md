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
J'ai trouvé la solution sur github : https://github.com/home-assistant/home-assistant/issues/27210#issuecomment-538891558

1. supprimer le fichier /config/.google.token
2. commenter l'intégration google dans votre fichier configuration.yaml
3. Redémarrer Home Assistant
4. remettre l'intégration google en décommentant les lignes de votre configuration.yaml
5. Redémarrer Home Assistant
6. Dans les notifications il vous sera demandé de vous reconnecter Home Assistant à votre compte Google pour autoriser l'accès
7. Voilà !
