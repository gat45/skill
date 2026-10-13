---
name: pivoting-tunneling
description: Pivot et tunneling — accès réseau interne via hôte compromis, SOCKS proxies, port forwarding, tunnels.
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

# Pivoting & tunneling

## Instructions
1. **Cartographier** : depuis l'hôte compromis `ip a`, `arp -a`, `ip route`, scan interne rapide.
2. **SSH pivot** : `ssh -D 1080 user@host` (SOCKS) + `proxychains4 nmap -sT -Pn <subnet>` ; ou `ssh -L 8080:internal:80 user@host`.
3. **Chisel** : `chisel server -p 8000 --reverse` / `chisel client <ip>:8000 R:socks` → SOCKS sur 1080.
4. **Ligolo-ng** : `ligolo-proxy -selfcert` côté attaquant, `ligolo-agent -connect <ip>:11601 -ignore-cert` côté cible, `interface_add` + route.
5. **Windows** : `plink -R 9999:localhost:9999`, `netsh interface portproxy add ...`, `socat`.
6. **Continuer** : `netexec smb <subnet>` via proxychains pour l'attaque AD à travers le tunnel.
7. Livrable : schéma du tunnel, accès internes obtenus, prochains hops.

## Règles
- Documenter chaque tunnel pour nettoyage. Ne pas dépasser le scope réseau autorisé.
