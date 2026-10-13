---
name: web-exploit
description: Exploitation des vulnérabilités web (OWASP Top 10) — injection SQL, XSS, SSRF, LFI/RFI, SSTI, upload, IDOR, auth bypass, RCE.
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

# Exploitation web

## Instructions
Tester méthodiquement chaque surface identifiée par /web-enum :
1. **Injection SQL** : manuel d'abord (`'`, `' OR 1=1-- -`, time-based), puis `sqlmap -u "<url>?param=1" -p param --batch --risk=2 --level=2`. Extraire uniquement le nécessaire pour la preuve.
2. **XSS** : `<script>alert(1)</script>`, `"><svg onload=alert(1)>`, contextes refléchis vs stockés, contournements d'encodage.
3. **SSRF** : `http://127.0.0.1:8080`, `file:///etc/passwd`, metadata cloud `http://169.254.169.254/latest/meta-data/`. Confirmer via callback contrôlé.
4. **LFI/RFI** : `php://filter/convert.base64-encode/resource=index.php`, `/etc/passwd`, traversée `../../`. LFI2RCE : log poisoning, session files.
5. **SSTI** : `{{7*7}}`, `${7*7}`, `<%= 7*7 %>` → détecter le moteur, puis RCE (ex. Jinja2).
6. **Upload** : `shell.php.jpg`, `.htaccess`, polyglottes, Content-Type spoof, test d'exécution réel + webshell minimal.
7. **IDOR/logique** : itérer IDs, changer méthodes (GET↔POST), mass assignment, race conditions (turbo-intruder).
8. **Auth** : JWT (`none`, alg confusion, clé faible — `jwt_tool`), session fixation, OAuth misconfig.
9. **Injection de commandes** : `;id`, `&&id`, `|id`, backticks, newlines ; out-of-band si aveugle.
10. Livrable : pour chaque vuln — PoC reproductible (requête curl exacte), impact, CVSS estimé.

## Règles
- Pas de dump massif de données perso — 1-2 lignes de preuve suffisent.
- Pas de defacement ni destruction de données. Toute RCE : documenter, ne pas pivoter hors scope.
