# Cahier de recette - RP02

> **Objectif :** valider la migration Open Source en conditions d'exploitation reelles, sans regression fonctionnelle.

## 1. Perimetre de recette

- Authentification LDAP/RADIUS.
- Application AAA sur equipements Cisco.
- Supervision metriques/logs via stack Docker.
- Services reseau annexes necessaires a l'exploitation (SNMP, syslog, DHCP/VRRP en contexte infra).

## 2. Tableau de tests

| ID | Axe | Type | Procedure executee | Resultat observe | Preuve | Statut |
|:--|:--|:--|:--|:--|:--|:--:|
| T01 | LDAP | CLI | Requete `ldapsearch` sur l'arbre LDAP | Arborescence OU et comptes retournes | `preuves/configs/exports-services-rp02.txt` | ✅ |
| T02 | RADIUS equipement | CLI | `test aaa group radius ...` sur switch | Authentification reussie | Sortie CLI fournie (switch) | ✅ |
| T03 | RADIUS local | CLI | `radtest` local | Test validé avec Access-Accept | `exports-services-rp02.txt` | ✅ |
| T04 | Cisco bascule | CLI | Verification declarations RADIUS/SNMP/syslog dans runconf | Configurations presentes et coherentes avec la migration | `runconf-cisco-*.txt` | ✅ |
| T05 | Monitoring metriques | CLI + GUI | Verification `docker ps` + dashboards Grafana | Stack monitoring active et exploitable | `exports-services-rp02.txt` | ✅ |
| T06 | Monitoring SNMP | CLI | Verification `prometheus.yml` + `snmp.yml` (`if_mib`, `auths`) | Cibles SNMP et module de collecte definis | `exports-services-rp02.txt` | ✅ |
| T07 | Logs centralises | CLI + GUI | Verification promtail + Loki + logging Cisco | Pipeline de centralisation present | `exports-services-rp02.txt` + `runconf-cisco-*.txt` | ✅ |

## 3. Synthese de recette

Le socle Open Source est operationnel pour l'authentification et la supervision, avec preuves de bascule cote equipements et de remontee de donnees monitoring/logs.

## 4. Decision

**Décision technique : validation opérationnelle sans réserves.**
