---
name: fuzzing-bruteforce
description: Fuzzing d'applications et brute force contrôlé — paramètres, endpoints, authentification, rate limiting respecté.
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

# Fuzzing & brute force

## Instructions
1. **Fuzzing web** : `ffuf -w <wordlist> -u https://<url>/FUZZ -t 30 -ac -o ffuf.json` ; paramètres : wordlist `burp-parameter-names.txt`.
2. **Wordlists** : `/usr/share/seclists/`, custom via `cewl -d 3 <site>`.
3. **Brute force auth** (UNIQUEMENT si confirmé + lockout policy connue) :
   - HTTP Basic : `hydra -L users.txt -P pass.txt <url> http-get /admin`
   - Form : `hydra -L users.txt -P pass.txt <ip> http-post-form "/login:user=^USER^&pass=^PASS^:F=incorrect"`
   - Threads bas (≤4), pauses, prod = 1 seule combinaison/user.
4. **Fuzzing binaire/protocole** : `boofuzz` sur sockets, `radamsa` sur fichiers, trier les crashes avec gdb.
5. Livrable : endpoints/params découverts, creds valides (si autorisé), crashes reproductibles.

## Règles
- Confirmation explicite avant tout brute force de credentials. Prod : jamais sans fenêtre de test définie.
