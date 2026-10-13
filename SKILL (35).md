---
name: recon-passive
description: Reconnaissance passive d'une cible (OSINT, sous-domaines, DNS, certificats, fuites techniques) sans contact direct. Utiliser au début d'un pentest autorisé.
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

# Reconnaissance passive

## Instructions
1. Confirmer la cible (domaine/organisation) et vérifier qu'elle correspond au périmètre autorisé fourni par l'utilisateur. Sinon, demander.
2. Collecte, dans l'ordre :
   - Sous-domaines : crt.sh (`curl -s "https://crt.sh/?q=%25.<domaine>&output=json" | jq -r '.[].name_value' | sort -u`), `subfinder -d <domaine> -silent`, `amass enum -passive -d <domaine>`
   - DNS : `dig ANY <domaine> @8.8.8.8`, SPF/DMARC (`dig TXT <domaine>`), MX/NS
   - Technos : `whatweb`, `httpx -tech-detect`
   - Exposition : GitHub dorking (`org:<org> password`, `filename:.env`, `filename:id_rsa`), buckets S3/GCS (`s3scanner`), Google dorks (`site:<domaine> intitle:index.of`)
   - Emails/personnes : `theHarvester -d <domaine> -b all`
   - Fuites d'identifiants : ne jamais utiliser des creds trouvés sans validation explicite ; signaler seulement leur existence.
3. Résoudre les sous-domaines : `httpx -l subdomains.txt -silent -title -status-code -tech-detect`.
4. Produire `recon-passive.md` : tableau cible/IP/techno/portée, puis proposer les axes actifs.

## Règles
- Aucun paquet vers la cible (scans actifs interdits ici).
- Toute donnée personnelle trouvée reste dans le livrable du client uniquement.
