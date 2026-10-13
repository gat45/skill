---
name: web-enum
description: Énumération web complète — fuzzing de répertoires, vhosts, paramètres, API, JS, et identification de CMS/frameworks.
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

# Énumération web

## Instructions
Pour chaque hôte web du scope :
1. Fingerprint : `whatweb -a3 <url>`, headers (`curl -sI <url>`), robots.txt, sitemap.xml, /.well-known/.
2. Répertoires/fichiers : `feroxbuster -u <url> -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,html,txt,json,bak -t 40 -o ferox.txt`
3. Vhosts : `gobuster vhost -u http://<ip> -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`.
4. Paramètres cachés : `arjun -u <url>`.
5. JS : extraire les scripts (LinkFinder, catjs), chercher endpoints et clés API (`grep -Ei 'api[_-]?key|token|secret'`).
6. API : si OpenAPI/Swagger trouvé (`/swagger.json`, `/openapi.json`, `/api-docs`), lister les routes et les tester.
7. CMS : `wpscan --url <url> --enumerate ap,at,u`, `droopescan`, `joomscan`.
8. Auth : pages login/registration/reset — noter pour /cred-attacks.
9. Livrable : endpoints triés (statut/longueur), techs+versions, points d'intérêt (uploads, admin, API).

## Règles
- Respecter le rate-limiting sur les cibles prod. Pas de brute force de mots de passe ici (voir /cred-attacks).
