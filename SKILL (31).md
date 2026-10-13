---
name: privesc-windows
description: Élévation de privilèges Windows — services, registry, AlwaysInstallElevated, UAC bypass, Potato, kernel, mimikatz.
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

# Privesc Windows

## Instructions
1. Récolte : `whoami /priv`, `whoami /groups`, `systeminfo`, services auto + `icacls` sur leurs binaires, registry `HKLM\...\Services`.
2. Automatisation : `winPEAS.exe`, `PowerUp.ps1 (Invoke-AllChecks)`, `SharpUp`.
3. Vecteurs :
   - **Unquoted service paths** + droits d'écriture
   - **Binaires de service modifiables** → remplacer, redémarrer
   - **AlwaysInstallElevated** → MSI malicieux (`msfvenom -f msi`)
   - **Autologon** : `reg query "HKLM\...\Winlogon"`
   - **PrintNightmare** selon patch level
   - **Potato family** (SweetPotato/GodPotato/RoguePotato) si SeImpersonate (typique IIS/SQL)
   - **UAC bypass** : fodhelper, computerdefaults
   - **Kernel** : `windows-exploit-suggester.py --systeminfo sysinfo.txt`
4. **Post-élévation** : `mimikatz` (`sekurlsa::logonpasswords`, `lsadump::dcsync`), `secretsdump.py` SAM/LSA, DPAPI.
5. Livrable : vecteur, commandes exactes, preuve SYSTEM.

## Règles
- Redémarrer les services après test. Pas de bypass EDR agressif sans demande.
