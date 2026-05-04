# Mode Opératoire - Gestion Open Source & Supervision

> **Auteur :** Edib Saoud
> **Date :** 02/03/2026 - 30/04/2026
> **Version :** 1.0
> **Cible :** Support Informatique / Administrateurs Réseaux

---

## 1. Gestion de l'Annuaire OpenLDAP

### 1.1 Ajouter un nouvel utilisateur
Contrairement à l'Active Directory, l'ajout d'utilisateurs natif se fait via fichier `.ldif`.
1. Créer un fichier `nouvel_utilisateur.ldif` :
```ldif
dn: uid=j.dupont,ou=Utilisateurs,dc=iris,dc=local
objectClass: inetOrgPerson
objectClass: posixAccount
uid: j.dupont
sn: Dupont
givenName: Jean
cn: Jean Dupont
userPassword: Password123
```
2. Appliquer la modification :
```bash
ldapadd -x -D "cn=admin,dc=iris,dc=local" -W -f nouvel_utilisateur.ldif
```

### 1.2 Vérifier la présence d'un utilisateur
Pour faire une recherche sur l'annuaire depuis la ligne de commande :
```bash
ldapsearch -x -b "dc=iris,dc=local" "(uid=j.dupont)"
```

---

## 2. Dépannage FreeRADIUS

**Cas d'usage :** Les authentifications Wi-Fi ou filaires (Cisco) sont rejetées.

1. Arrêter le service tournant en tâche de fond :
   `sudo systemctl stop freeradius`
2. Lancer FreeRADIUS en **Mode Débogage (Debug)** pour voir la transaction en direct :
   `sudo freeradius -X`
3. Analyser la sortie :
   - Si la ligne affiche `Access-Accept` : C'est le switch Cisco qui bloque.
   - Si la ligne affiche `Access-Reject` ou une erreur de liaison LDAP : L'identifiant est incorrect, ou la connexion avec OpenLDAP a échoué.
4. Une fois le test terminé (Ctrl+C), relancer le service normalement :
   `sudo systemctl start freeradius`

---

## 3. Accès à la Supervision (Grafana)

**Cas d'usage :** Surveillance de l'état des ports du Switch et de la charge du serveur Debian.

1. Ouvrir le navigateur et se rendre sur `http://192.168.50.100:3000`.
2. S'authentifier (Identifiants fournis dans la base de mots de passe de l'école).
3. Se rendre dans le menu **Dashboards**.
   - Le dashboard "Node Exporter" remonte la RAM, le CPU et le disque du serveur Debian.
   - Le dashboard "Cisco SNMP" affiche la bande passante utilisée sur chaque port Gigabit du switch et les éventuelles pertes de paquets.
4. Les journaux d'événements FreeRADIUS sont consultables directement dans Grafana via le composant **Loki** (Menu *Explore*).
