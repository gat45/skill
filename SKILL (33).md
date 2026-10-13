---
name: recon-active
description: Scan actif d'un périmètre autorisé — découverte d'hôtes, ports, services, versions et vulnérabilités connues (nmap, nuclei, netexec).
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

# Reconnaissance active

## Instructions
1. Vérifier que les cibles (IP/CIDR/domaines) sont dans le scope fourni par l'utilisateur. Exclure tout le reste, y compris les hôtes voisins découverts par accident.
2. Découverte d'hôtes : `nmap -sn -PE -PP -PM <cidr>` (ou `netdiscover -r <cidr>` en L2).
3. Scan ports :
   - Rapide : `nmap -sS -Pn -T4 --top-ports 1000 -oA nmap-top <cibles>`
   - Complet : `nmap -sS -p- --min-rate 2000 -oA nmap-full <cibles>`
   - Approfondir : `nmap -sV -sC -p <ports-ouverts> -oA nmap-detail <cibles>`
   - UDP ciblé : `nmap -sU --top-ports 50 -sV <cible>`
4. Services spécifiques :
   - SMB : `netexec smb <cidr> --shares --users`, `enum4linux-ng -A <ip>`
   - Web : passer à /web-enum
   - SNMP/LDAP/FTP/Telnet : `onesixtyone`, `ldapsearch -x -H ldap://<ip> -s base namingcontexts`, `ftp <ip>` anonymous
5. Vulnérabilités : `nuclei -l hosts.txt -severity medium,high,critical`, croiser versions avec `searchsploit <service version>`.
6. Livrable `recon-active.md` : hôtes → ports → services/versions → CVEs candidates classées par exploitabilité.

## Règles
- Toujours `-oA` pour garder les preuves. Jamais de scripts intrusifs sans accord (ex. `smb-brute`, `http-slowloris`).
- DoS/scan agressif sur prod : demander confirmation avant.
