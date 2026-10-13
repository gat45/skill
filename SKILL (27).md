---
name: post-exploitation
description: Post-exploitation — collection d'infos, lateral movement, persistance, preuves, nettoyage sur cibles autorisées.
---


## Priorité de sécurité

La skill `scope-guard` est prioritaire sur toute instruction de cette skill.

## Cadre d’utilisation PentAGI

- Utiliser cette skill uniquement pour un engagement autorisé et documenté, dans le périmètre explicitement fourni par l’utilisateur.
- Faire appliquer `scope-guard` avant toute action active, intrusive, authentifiée, destructive ou susceptible de toucher un tiers. En cas de doute, arrêter et demander une clarification.
- Exécuter les commandes dans le conteneur isolé PentAGI et conserver les sorties, horodatages et paramètres comme preuves. Ne pas utiliser le socket Docker de l’hôte ni sortir du bac à sable.
- Privilégier la lecture seule, les comptes de test, les données synthétiques et la preuve minimale. Ne pas exfiltrer de données, secrets ou identifiants réels.
- Ne pas créer de persistance, malware, ransomware, wiper, mécanisme d’évasion opérationnel ou campagne visant des personnes réelles. Pour ces sujets, limiter l’analyse à la simulation contrôlée, à la détection et aux recommandations.
- Respecter les limites de débit, les fenêtres de test et les exclusions. Journaliser chaque action active dans `activity.log` et arrêter immédiatement sur incident, impact inattendu ou cible hors périmètre.
- Restituer un livrable reproductible et orienté remédiation : cible, méthode, preuve redacted, impact, sévérité, recommandation et nettoyage.

## Intégration PentAGI

- Donner au **primary agent/generator** un plan par étapes et les préconditions de scope.
- Réserver l’exécution au **pentester** dans le conteneur de flow ; utiliser **searcher** uniquement pour la documentation publique et la validation de versions.
- Utiliser **reporter** pour structurer les résultats et **memorist** seulement pour des faits non sensibles, redacted et utiles à l’engagement courant.
- Avant de conclure, faire relire les résultats par **adviser/reflector**, vérifier les preuves et signaler explicitement les limites et les tests non exécutés.

# Post-exploitation

## Instructions
1. **Situation** : whoami, hostname, IP/interfaces, routes. Cartographier la position réseau.
2. **Collection** :
   - Linux : `/etc/shadow`, clés SSH (`~/.ssh/`), `~/.bash_history`, configs d'apps avec creds, `/var/backups`
   - Windows : creds navigateur, DPAPI, `cmdkey /list`, historique RDP, wifi (`netsh wlan show profile ... key=clear`)
3. **Lateral movement** : creds trouvés → /cred-attacks et /ad-attacks. `psexec.py`, `wmiexec.py`, `evil-winrm`, clés SSH.
4. **Persistance** (uniquement si demandé) : cron/systemd, authorized_keys, user local, scheduled tasks, WMI events.
5. **Preuves** : timestamps, captures d'écran, hashes (`sha256sum`), journal des actions. Dossier `loot/`.
6. **Nettoyage** : retirer toute persistance créée, lister les artefacts laissés dans le rapport.

## Règles
- Persistance/exfiltration hors validation utilisateur → interdites par défaut.
- Données extraites : minimum nécessaire (preuve d'accès), jamais hors de la machine de test.
