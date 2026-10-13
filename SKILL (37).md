---
name: redteam-evasion
description: Techniques red team — évasion AV/EDR, obfuscation de payloads, exécution LOLBin, AMSI bypass, chargeurs mémoire.
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

# Red team / évasion

## Instructions
1. **Recon EDR/AV** : identifier les produits (`Get-CimInstance -Namespace root/SecurityCenter2`, services).
2. **Payloads** :
   - `msfvenom ... -f raw -e x64/xor_dynamic -i 10` + stager custom (jamais le exe brut, signé)
   - Shellcode runner : `VirtualAlloc` + `CreateThread` en C#/C, `sRDI`/`Donut`
   - Obfuscation .NET : `InvisibilityCloak`
3. **LOLBins** : `powershell -nop -w hidden -enc <b64>`, `mshta`, `rundll32`, `certutil -urlcache -split -f`, `regsvr32`, `wmic process call create`.
4. **AMSI** : patch mémoire (`AmsiScanBuffer` hook) ou downgrade PS. Documenter la technique.
5. **Exécution en mémoire** : `Invoke-ReflectivePEInjection`, `inlineExecute-Assembly` (BOF).
6. **Opsec** : horaires de test, jitter C2, HTTPS vers infra dédiée, rotation de domaines.
7. Livrable : techniques utilisées, détections déclenchées, recommandations (YARA/Sigma) pour chaque bypass réussi.

## Règles
- Objectif = évaluer la détection, pas nuire. Restituer les règles au Blue Team. Jamais de ransomware/wiper.
