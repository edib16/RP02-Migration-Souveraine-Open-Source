# Dossier de choix technique - RP02

> **Auteur :** Edib Saoud  
> **Version :** 2.0  
> **Statut :** Documentation consolidée

## 1. Contexte et objectif technique

Le projet répond à un objectif de souveraineté numérique : remplacer une chaîne propriétaire d'authentification réseau par une chaîne Open Source exploitable localement.

Les objectifs fonctionnels sont :

1. Assurer l'authentification réseau (filaire et Wi-Fi) via LDAP/RADIUS.
2. Conserver la continuité de service pendant la bascule.
3. Ajouter une supervision unifiée (métriques + logs) exploitable par l'équipe IT.

## 2. Existant constaté avant migration

- Équipements d'accès Cisco déjà en production.
- Dépendance historique à un serveur d'authentification propriétaire.
- Besoin d'une bascule progressive (cohabitation ancien/nouveau RADIUS observée dans les `runconf`).

## 3. Solutions retenues et justification

| Besoin | Solution retenue | Justification |
|:--|:--|:--|
| Annuaire centralisé | OpenLDAP | Standard ouvert, interopérable avec FreeRADIUS |
| Contrôle d'accès 802.1X | FreeRADIUS | Compatible équipements Cisco et VLAN dynamiques |
| Supervision métriques | Prometheus + exporters | Collecte unifiée serveur + équipements réseau |
| Visualisation | Grafana | Exploitation opérationnelle et support recette |
| Centralisation logs | Loki + Promtail | Corrélation événements réseau/services |
| Services réseau annexes | Kea DHCP + Keepalived/VRRP | Stabilité d'exploitation sur la topologie cible |
| Socle système | Debian 12 | Cohérence, stabilité, maintenance simplifiée |

## 4. Architecture logique cible

### 4.1 Plan fonctionnel

1. Les terminaux s'authentifient sur les équipements Cisco.
2. Les équipements interrogent FreeRADIUS.
3. FreeRADIUS vérifie l'identité via OpenLDAP.
4. Les règles de groupe appliquent les attributions réseau (VLAN dynamiques).
5. Les métriques/logs remontent vers Prometheus/Loki puis Grafana.

### 4.2 Niveaux d'administration

- **CLI** : Debian, FreeRADIUS, OpenLDAP, Docker, Cisco.
- **GUI** : Grafana uniquement (sources, dashboards, consultation logs).

### 4.3 Description de l'architecture (Schéma logique)

L'architecture se décompose en trois segments principaux :

1.  **Segment Accès (Cisco)** : Composé d'un commutateur 2960 (SW1), d'un routeur 2911 (R1) et d'une borne Wi-Fi (AP1). Ces équipements agissent comme des "Authenticators" 802.1X.
2.  **Segment Services (Debian)** : Un serveur central hébergeant la chaîne de confiance Open Source :
    *   **FreeRADIUS** : Traite les requêtes Access-Request via le protocole EAP-PEAP.
    *   **OpenLDAP** : Sert de base d'utilisateurs unique (référentiel d'identité).
    *   **Docker** : Isole la stack de supervision (Prometheus, Grafana, Loki).
3.  **Segment Flux** :
    *   Flux **AAA** (RADIUS/UDP 1812-1813) entre Cisco et Debian.
    *   Flux **LDAP** (TCP 389) en local sur le serveur Debian.
    *   Flux **Supervision** (SNMP/UDP 161) pour la collecte des métriques réseau.

## 5. Risques et mesures retenues

| Risque | Impact | Traitement |
|:--|:--:|:--|
| Rupture d'authentification | Élevé | Bascule progressive et coexistence temporaire ancien/nouveau RADIUS |
| Erreur de mapping utilisateurs/groupes | Moyen | Vérifications LDAP et règles de groupes dans `inner-tunnel` |
| Perte de visibilité pendant migration | Élevé | Mise en place monitoring/logging avant validation finale |
| Exposition d'informations sensibles | Élevé | Politique de masquage strict dans la documentation publique |

## 6. Preuves utilisées

- `preuves/configs/runconf-cisco-sw1-iris.txt`
- `preuves/configs/runconf-cisco-r1-iris.txt`
- `preuves/configs/runconf-cisco-ap1-iris.txt`
- `preuves/configs/runconf-debian-vrouteur-iris.txt`
- `preuves/configs/exports-services-rp02.txt`
