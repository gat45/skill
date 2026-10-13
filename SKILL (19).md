---
name: mobile-pentest
description: Pentest mobile Android/iOS — décompilation, analyse statique/dynamique, interception de trafic, SSL pinning bypass, stockage sensible.
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

# Pentest mobile

## Instructions
1. **Android — statique** :
   - `apktool d app.apk`, `jadx-gui app.apk`, `aapt dump badging app.apk`
   - Chercher `debuggable`, `allowBackup`, cleartext (`usesCleartextTraffic`)
   - Secrets : `grep -REi 'api[_-]?key|secret|password|AKIA[0-9A-Z]{16}' app/`
2. **Android — dynamique** :
   - `adb install app.apk`, `adb logcat`
   - Proxy mitmproxy/Burp + CA système (MagiskTrustUserCerts sur Android ≥7)
   - SSL pinning bypass : `objection -g <pkg> explore` → `android sslpinning disable`, ou Frida
   - Hooks : root/debug detection, crypto
3. **Android — données** : `adb backup`, `/data/data/<pkg>/` (root) → SQLite, SharedPrefs, tokens en clair.
4. **iOS — statique** : `class-dump-z`, `otool -ov`, Info.plist (ATS), unzip `.ipa`.
5. **iOS — dynamique** : jailbreak, `objection -g <bundle> explore`, Frida, proxy Burp.
6. **Checks OWASP MASVS** : stockage, crypto, auth, IPC exporté, deep links (`adb shell am start -d "scheme://..."`).
7. Livrable : vulns classées (secret leak, transport clair, pinning absent, debuggable), PoC + captures.

## Règles
- Tester uniquement l'app fournie/autorisée. Ne pas redistribuer les secrets trouvés.
