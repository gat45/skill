---
name: scada-ot
description: Pentest ICS/SCADA/OT — découverte Modbus/S7/PLC, fingerprinting de protocoles industriels, sûreté avec prudence maximale.
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

# Pentest ICS/SCADA (OT)

## Instructions
1. **Prudence d'abord** : en OT, la DISPONIBILITÉ avant tout. Confirmé avec l'utilisateur : aucune écriture sur PLC/HMI, aucune commande de process. Lecture seule.
2. **Découverte** : `nmap -sT -Pn -p 102,44818,20000,47808,502,4128,24576-24579,34962-34964,19000,135,445 <subnet>` ; `nmap --script modbus-discover,s7-info,enip-info,bacnet-info <ip>`.
3. **Fingerprint** : Modbus TCP (502), S7 (102), EtherNet/IP (44818), DNP3 (20000), BACnet (47808), OPC UA (4840).
4. **Énumération** :
   - Modbus : `mbpoll`/`pymodbus` read coils/registers (lecture seule)
   - S7 : `snap7` read (DB, merker), version CPU
5. **Passive** : `tshark -i <iface> -f "port 502 or port 102"` sur port miroir.
6. **Sûreté** : segmentation IT/OT, accès distants, firmwares vulnérables (ICS-CERT), mots de passe défaut.
7. Livrable : inventaire automates, registres lisibles, risques, recommandations (IEC 62443, Purdue model).

## Règles
- INTERDIT : écrire dans des registres, arrêter/forcer des équipements, flood Modbus. Tout test actif validé par l'ingénieur process du client.
