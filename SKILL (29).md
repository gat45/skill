---
name: privesc-linux
description: Élévation de privilèges Linux — SUID, sudo, cron, capabilities, kernel, NFS, conteneurs.
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

# Privesc Linux

## Instructions
1. Récolte : `id`, `sudo -l`, `getcap -r / 2>/dev/null`, `find / -perm -4000 -type f 2>/dev/null`, `crontab -l`, `ls -la /etc/cron*`, `ss -tlnp`, `mount`, `lsmod`.
2. Automatisation : `linpeas`, `unix-privesc-check`.
3. Analyser :
   - **sudo** : GTFOBins pour chaque binaire autorisé ; CVE sudo (Baron Samedit, etc.)
   - **SUID** : binaires inhabituels, `find -exec`
   - **Cron** : PATH hijack, wildcard injection (`tar --checkpoint`), fichiers modifiables
   - **Capabilities** : `cap_setuid` (python/perl), `cap_net_raw`
   - **Interne** : ports locaux, configs avec creds, NFS no_root_squash (`showmount -e`)
   - **Kernel** : `uname -a` → PwnKit (CVE-2021-4034), Dirty Pipe, etc. Pas de crash sans accord.
   - **Docker** : socket `/var/run/docker.sock` montée → escape (voir /container-escape)
4. Exécuter le vecteur le plus fiable, vérifier `id`, stabiliser le shell.
5. Livrable : vecteur exact, commandes, preuve root.

## Règles
- Pas de kernel exploit destructif sans confirmation. Noter tout changement pour rollback.
