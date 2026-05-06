# Procedure d'installation - RP02

> **Principe:** cette procedure suit la chronologie reelle du projet (par phases), avec separation **CLI** / **GUI**.

## 1. Phase 1 - Preparation du socle Debian (CLI)

1. Installer Debian 12 sur VM dediee.
2. Appliquer la configuration reseau de base.
3. Mettre le systeme a jour.

Commandes type (variables masquees) :

```bash
sudo apt update && sudo apt upgrade -y
```

## 2. Phase 2 - Deploiement OpenLDAP + FreeRADIUS (CLI)

### 2.1 Installation des composants

```bash
sudo apt install slapd ldap-utils -y
sudo apt install freeradius freeradius-ldap freeradius-utils -y
```

### 2.2 Configuration OpenLDAP

- Domaine LDAP configure : `dc=<DOMAINE>,dc=<LOCAL>`.
- Arborescence organisationnelle creee (ex. Etudiants, Profs, Administration).

Preuve : `preuves/configs/exports-services-rp02.txt` (requetes LDAP et configuration FreeRADIUS liee au LDAP).

### 2.3 Configuration FreeRADIUS

Fichiers confirmes :

- `/etc/freeradius/3.0/clients.conf`
- `/etc/freeradius/3.0/mods-enabled/ldap`
- `/etc/freeradius/3.0/mods-enabled/eap`
- `/etc/freeradius/3.0/sites-enabled/default`
- `/etc/freeradius/3.0/sites-enabled/inner-tunnel`

Points techniques constates :

- Authentification EAP-PEAP.
- Liaison LDAP active.
- Attribution VLAN selon OU LDAP (`inner-tunnel`).

*Note : La configuration est validée par les fichiers de configuration présents dans `exports-services-rp02.txt` (sections FreeRADIUS et LDAP).*

## 3. Phase 3 - Migration AD -> LDAP (CLI)

Realise :

1. Conversion de l'annuaire historique AD vers LDIF.
2. Import dans OpenLDAP.
3. Validation de l'arborescence et des comptes.

Fichiers de migration identifies dans `preuves/configs/` :

- `users.ldif`
- `all_users.ldif`
- `creation_comptes.ldif`
- `structure.ldif`

*Note : La migration a été effectuée via un script de conversion personnalisé (LDIF) permettant d'importer les objets AD dans la structure OpenLDAP cible.*

## 4. Phase 4 - Bascule des equipements Cisco (CLI)

Realise :

1. Mise a jour des `runconf` pour pointer vers le nouveau RADIUS.
2. Coexistence temporaire ancien/nouveau RADIUS selon equipements.
3. Activation/verification AAA, dot1x, SNMP et syslog.

Preuves :

- `preuves/configs/runconf-cisco-sw1-iris.txt`
- `preuves/configs/runconf-cisco-r1-iris.txt`
- `preuves/configs/runconf-cisco-ap1-iris.txt`
- `preuves/configs/runconf-debian-vrouteur-iris.txt`

## 5. Phase 5 - Supervision et observabilite (CLI + GUI)

### 5.1 CLI - Stack Docker monitoring/logs

Composants deployes :

- Prometheus
- Grafana
- Loki
- Promtail
- Node Exporter
- SNMP Exporter

Configuration verifiee dans :

- `preuves/configs/exports-services-rp02.txt` (`docker-compose.yml`, `prometheus.yml`, `snmp.yml`, `promtail-config.yml`)

### 5.2 GUI - Grafana

Actions effectuees :

1. Connexion a l'interface Grafana.
2. Verification des sources de donnees.
3. Consultation des dashboards (SNMP, Node Exporter, logs Cisco centralises).

*Note : L'interface Grafana confirme la remontée des métriques SNMP (Cisco) et système (Node Exporter) via les tableaux de bord importés.*

## 6. Phase 6 - Validation technique (CLI + GUI)

- **CLI** : tests AAA/LDAP, verification des reponses d'authentification.
- **GUI** : verification de la remontee metriques et logs.

Preuve positive disponible :

- Test AAA sur switch Cisco avec authentification reussie.

*Note : La preuve `radtest` est consignée dans le fichier `exports-services-rp02.txt` (Section 6).*
