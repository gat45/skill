---
name: report-writer
description: Rédaction du rapport de pentest professionnel — executive summary, findings avec CVSS, PoCs, recommandations prioritaires.
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

# Rédaction de rapport

## Instructions
Générer un rapport structuré (français ou langue du client) :
1. **Executive summary** (non technique) : périmètre, méthode, top 3 risques métier, posture globale.
2. **Méthodologie** : outils, dates, périmètre exact, limitations.
3. **Findings** (par CVSS 3.1 décroissante) : titre, sévérité + vecteur CVSS, description, impact métier, preuve (redacted), recommandation + références (OWASP, CWE, CIS).
4. **Annexes** : inventaire hôtes/services testés, creds obtenus (chiffrés), artefacts.
5. Générer `report.md`, proposer DOCX (`pandoc report.md -o report.docx`).
6. Ton : factuel, orienté remédiation, chaque finding reproductible.

## Format CVSS
Calculateur https://www.first.org/cvss/calculator/3.1 — vecteur complet (ex. CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H → 9.8).
