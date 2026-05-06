# RP02 - Migration Souveraine Open Source

> **Auteur :** Edib Saoud  
> **Période :** 02/03/2026 -> 30/04/2026  
> **Contexte :** BTS SIO SISR - IRIS Mediaschool

Ce dépôt documente la migration d'une chaîne d'authentification propriétaire vers une architecture Open Source, avec supervision centralisée et exploitation réseau associée.

## 1. Périmètre réellement réalisé

1. **Identité et authentification** : OpenLDAP + FreeRADIUS sur Debian 12.
2. **Accès réseau** : bascule des équipements Cisco (switch, routeur, AP) vers le nouveau serveur RADIUS.
3. **Supervision et logs** : Docker, Prometheus, Grafana, Loki, Promtail, Node Exporter, SNMP Exporter.
4. **Services annexes intégrés au RP02** : Kea DHCP et Keepalived/VRRP (contexte d'exploitation réseau).

## 2. Chronologie réelle (par phases)

1. **Socle Debian** : installation Debian 12 et configuration de base.
2. **Chaîne Open Source** : déploiement FreeRADIUS + OpenLDAP.
3. **Migration annuaire** : conversion AD vers LDAP et import des entrées.
4. **Bascule réseau** : changement des équipements Cisco vers le nouveau RADIUS.
5. **Supervision** : déploiement stack Docker monitoring/logging.
6. **Recette** : validation AAA sur équipements et vérifications d'observabilité.

## 3. Distinction CLI vs GUI

| Domaine | CLI (réalisé) | GUI (réalisé) |
|:--|:--|:--|
| Déploiement OpenLDAP/FreeRADIUS | Installation, configuration, tests LDAP/RADIUS | - |
| Bascule Cisco | `runconf`, AAA, RADIUS, SNMP, syslog | - |
| Monitoring | Docker Compose, Prometheus, promtail, snmp-exporter | Grafana (sources + dashboards) |
| Recette | `test aaa`, `ldapsearch`, vérifications de services | Contrôle visuel dashboards/logs Grafana |

## 4. Dossiers documentaires

1. [01_DOSSIER_CHOIX_TECHNIQUE.md](01_DOSSIER_CHOIX_TECHNIQUE.md)
2. [02_PROCEDURE_INSTALLATION.md](02_PROCEDURE_INSTALLATION.md)
3. [03_MODE_OPERATOIRE.md](03_MODE_OPERATOIRE.md)
4. [04_CAHIER_DE_RECETTE.md](04_CAHIER_DE_RECETTE.md)

## 5. Preuves techniques et nomenclature

### 5.1 Configurations (uniformisées)

- `preuves/configs/runconf-cisco-sw1-iris.txt`
- `preuves/configs/runconf-cisco-r1-iris.txt`
- `preuves/configs/runconf-cisco-ap1-iris.txt`
- `preuves/configs/runconf-debian-vrouteur-iris.txt`
- `preuves/configs/exports-services-rp02.txt`

### 5.2 Document de synthèse

- [Fiche RP02 Edib Saoud.pdf](Fiche%20RP02%20Edib%20Saoud.pdf)


## 6. Masquage des données sensibles (strict)

Dans les documents, les éléments suivants sont masqués ou remplacés par des variables :

- secrets/mots de passe/clefs ;
- communautés SNMP ;
- identités nominatives ;
- domaines externes ;
- adresses IP détaillées.

