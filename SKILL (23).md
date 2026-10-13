---
name: phishing-simulation
description: Simulation de phishing autorisée — campagne contrôlée (GoPhish), pages de leurre, tracking, métriques, sensibilisation.
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

# Simulation de phishing

## Instructions
1. **Autorisation écrite obligatoire** : liste des destinataires, fenêtre, domaines de leurre autorisés. Sinon, ne pas démarrer.
2. **Infrastructure** : GoPhish (`docker run -p 3333:3333 gophish/gophish`), domaine dédié + SPF/DKIM, landing clone du SSO client (logs les saisies sans les transmettre).
3. **Templates** : scénarios réalistes (réinit mdp, facture, RH), payloads : lien tracking, pièce jointe (.docm macro inerte, .lnk), QR code.
4. **Exécution** : vagues, tracking opens/clicks/soumissions/reports.
5. **Résultats** : tableau par département, clics vs soumissions, temps avant signalement.
6. **Livrable** : métriques, recommandations (MFA, DMARC p=reject, formation), SUPPRESSION de toute cred capturée (jamais réutilisée).

## Règles
- Jamais de vraies creds réutilisées. Pas de cibles hors liste. Prévenir le SOC du client (ou l'intégrer au test du SOC, au choix du client).
