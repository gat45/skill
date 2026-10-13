---
name: forensics
description: Analyse forensique et triage — artefacts Windows/Linux, memory dump, logs, timeline d'incident, IOCs.
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

# Forensique / DFIR

## Instructions
1. **Acquisition** : image disque (`dc3dd`, `dd`), dump mémoire (`winpmem`), hashes (`sha256sum`), chaîne de custody.
2. **Windows** :
   - Timeline : log2timeline/plaso, parsing `$MFT` (analyzeMFT)
   - Artefacts : RegRipper (Run, UserAssist, ShimCache/AmCache, USBSTOR)
   - Logs : `Get-WinEvent -FilterHashtable @{LogName='Security';Id=4624,4625,4720,4688}`, PowerShell operational
   - Mémoire : `volatility3 -f mem.dump windows.pslist`, `malfind`, `netscan`, `cmdline`
3. **Linux** : `/var/log/auth.log`, `last`, cron, nouveaux users, `.bash_history`, `find / -mtime -7 -type f`.
4. **Fichiers suspects** : `file`, `exiftool`, YARA, `strings -el`, `peepdf`.
5. **Réseau** : `tshark -r capture.pcap`, Zeek.
6. Livrable : timeline, IOCs (hashes, domaines, IPs, TTPs MITRE), recommandations containment.

## Règles
- Ne jamais modifier l'original ; travailler sur copie. Ne pas supprimer de fichiers sans validation.
