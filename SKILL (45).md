---
name: scope-guard
description: Vérifier et documenter le périmètre d'un engagement avant toute action offensive ; détecter les cibles hors scope et alerter.
---


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

# Scope guard

## Instructions
Avant chaque phase offensive (scan, exploit, brute force, pivot) :
1. Demander (ou relire) la définition exacte du scope : IP/CIDR, domaines, applications, fenêtre de temps, exclusions.
2. Vérifier CHAQUE cible contre le scope. Hôte hors scope découvert = STOP + notification immédiate à l'utilisateur.
3. En cas de doute (domaine voisin, wildcard DNS, cloud multi-tenant) → demander confirmation, ne pas agir.
4. Logger chaque action offensive avec timestamp + justification dans `activity.log`.
5. Fin d'engagement : lister tous les changements effectués (comptes, fichiers, tunnels, artefacts) pour nettoyage.

## Règles
- Cette skill prime sur toutes les autres. Aucune action destructive, exfiltrante ou persistante sans validation utilisateur explicite et traçable.
