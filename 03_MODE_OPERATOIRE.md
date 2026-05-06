# Mode operatoire - RP02

> **Public cible :** support informatique / administration reseau  
> **Perimetre :** OpenLDAP, FreeRADIUS, equipements Cisco, stack monitoring

## 1. Exploitation courante

| Action | CLI | GUI |
|:--|:--|:--|
| Verification etat services | `systemctl status` / `docker ps` | - |
| Controle authentification | `test aaa`, `ldapsearch`, `radtest` | - |
| Consultation metriques | - | Grafana dashboards |
| Consultation logs | fichiers + promtail/loki | Grafana Explore |

## 2. Gestion annuaire LDAP (CLI)

### 2.1 Ajouter un utilisateur (mode LDIF)

```ldif
dn: uid=<UID_UTILISATEUR>,ou=<OU_CIBLE>,dc=<DOMAINE>,dc=<LOCAL>
objectClass: inetOrgPerson
objectClass: posixAccount
uid: <UID_UTILISATEUR>
sn: <NOM_MASQUE>
givenName: <PRENOM_MASQUE>
cn: <CN_MASQUE>
userPassword: <MOT_DE_PASSE_MASQUE>
```

```bash
ldapadd -x -D "cn=admin,dc=<DOMAINE>,dc=<LOCAL>" -W -f nouvel_utilisateur.ldif
```

### 2.2 Verifier une entree

```bash
ldapsearch -x -LLL -H ldap://localhost -b "dc=<DOMAINE>,dc=<LOCAL>" "(uid=<UID_UTILISATEUR>)" dn cn uid
```

## 3. Gestion FreeRADIUS (CLI)

### 3.1 Verifier la configuration

Fichiers de reference :

- `/etc/freeradius/3.0/clients.conf`
- `/etc/freeradius/3.0/mods-enabled/ldap`
- `/etc/freeradius/3.0/mods-enabled/eap`
- `/etc/freeradius/3.0/sites-enabled/default`
- `/etc/freeradius/3.0/sites-enabled/inner-tunnel`

### 3.2 Diagnostic d'authentification

```bash
sudo systemctl stop freeradius
sudo freeradius -X
```

Interpretation :

- `Access-Accept` : authentification valide.
- `Access-Reject` : identifiants invalides ou regle LDAP/RADIUS non satisfaite.

Retour normal :

```bash
sudo systemctl start freeradius
```

## 4. Exploitation reseau Cisco (CLI)

Controles prioritaires :

1. Presence des serveurs RADIUS declares.
2. Activation AAA/dot1x.
3. Envoi syslog vers la plateforme de logs.
4. Configuration SNMP de supervision.

Preuves techniques : fichiers `preuves/configs/runconf-*.txt`.

## 5. Supervision et observabilite

### 5.1 CLI - stack monitoring

```bash
cd ~/monitoring
docker ps
```

Verifier la presence des conteneurs : prometheus, grafana, loki, promtail, node-exporter, snmp-exporter.

### 5.2 GUI - Grafana

1. Ouvrir Grafana via l'URL d'exploitation.
2. Verifier les dashboards :
   - metriques SNMP reseau ;
   - metriques Node Exporter ;
   - centralisation logs (Loki/Promtail).

## 6. Sauvegarde et securite (exploitation)

- Sauvegarder regulierement les LDIF et fichiers de configuration.
- Restreindre l'acces aux fichiers contenant des secrets.
- Ne jamais publier de sortie brute non masquee dans la documentation.

