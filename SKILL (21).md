---
name: network-pentest
description: Exploitation des services réseau — SMB, RDP, LDAP, MSSQL, FTP, SMTP, SNMP sur des hôtes autorisés.
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

# Pentest réseau

## Instructions
Pour chaque service de /recon-active :
1. **SMB (445)** : `netexec smb <ip>` (null session, signatures), `smbclient -L //<ip> -N`, énumération shares+users. Écriture sur share ? → capture de hashes (voir /cred-attacks).
2. **RDP (3389)** : `netexec rdp <ip>`, vérifier NLA, brute force si autorisé.
3. **MSSQL (1433)** : `netexec mssql <ip> -u <u> -p <p> -q 'SELECT @@version'`, `xp_cmdshell` si creds.
4. **LDAP (389/636)** : `ldapsearch -x -H ldap://<ip> -b "dc=dom,dc=loc"`, anonymous bind, users/computers/descriptions.
5. **FTP (21)** : anonymous login, fichiers intéressants.
6. **SMTP (25)** : `smtp-user-enum -M VRFY -U users.txt -t <ip>`.
7. **SNMP (161/udp)** : `onesixtyone` (community strings), `snmpwalk -v2c -c public <ip>`.
8. **Vulns** : `searchsploit <service> <version>`, `msfconsole` si module fiable, modules netexec.
9. Livrable : creds récupérés, accès obtenus, prochaines étapes (pivot ou privesc).

## Règles
- Brute force : demander accord explicite + lockout policy avant. Jamais de DoS.
