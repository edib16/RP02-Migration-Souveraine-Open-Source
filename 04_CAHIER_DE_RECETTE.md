# Cahier de Recette et Tests - Migration Souveraine

> **Auteur :** Edib Saoud
> **Date :** 02/03/2026 - 30/04/2026
> **Version :** 1.0
> **Statut :** Validé

---

## 1. Objectifs de la Recette

L'objectif de cette recette est de certifier que le remplacement de la solution d'authentification propriétaire par la "Stack Libre" (FreeRADIUS + OpenLDAP) n'entraîne aucune régression technique. Le contrôle d'accès doit demeurer fonctionnel et la supervision Grafana opérationnelle.

## 2. Tableau de Recette Infrastructure

| N° | Catégorie | Scénario de Test | Procédure | Résultat Attendu | Statut |
|:---|:---|:---|:---|:---|:---:|
| **T1** | **Annuaire** | Recherche LDAP | Lancer la commande `ldapsearch` sur un compte de test depuis le serveur Debian. | Le serveur LDAP répond immédiatement en renvoyant les attributs corrects du compte de test (uid, cn). | ✅ Validé |
| **T2** | **RADIUS** | Test local `radtest` | Lancer `radtest identifiant mdp localhost 0 testing123` depuis le serveur. | Le composant FreeRADIUS interroge le LDAP et renvoie explicitement un `Access-Accept`. | ✅ Validé |
| **T3** | **Cisco** | Bascule du RADIUS | Se connecter au réseau filaire 802.1X via le switch Cisco avec un PC de test et des identifiants LDAP valides. | Le switch Cisco dialogue avec `192.168.50.100`. Le port s'ouvre (`AUTHORIZED`), prouvant que la migration Cisco ↔ FreeRADIUS fonctionne. | ✅ Validé |
| **T4** | **Rejet** | Identifiants invalides | Tenter de se connecter au port avec un mot de passe délibérément faux. | Le switch bloque le port. En debug (`freeradius -X`), on observe le message `Access-Reject`. | ✅ Validé |
| **T5** | **Monitoring** | Collecte CPU Debian | Consulter le dashboard "Node Exporter" sur Grafana (`http://192.168.50.100:3000`). | Les graphiques s'actualisent en temps réel, confirmant que Prometheus récupère bien les données système. | ✅ Validé |
| **T6** | **Monitoring** | Collecte SNMP Cisco | Consulter le dashboard réseau SNMP. Débrancher/rebrancher un port sur le switch. | Le dashboard Grafana affiche les compteurs de trames et l'état UP/DOWN du port Cisco concerné. | ✅ Validé |

---

## 3. Synthèse de la Recette

L'objectif de souveraineté fixé par le groupe Mediaschool est atteint :
- La chaîne d'authentification complète repose désormais sur des briques Open Source communautaires (LDAP v3 et RADIUS).
- La supervision proactive intégrée permet à l'équipe IT d'anticiper les dysfonctionnements (saturation de RAM, chute de port) via l'interface Grafana unifiée.

**Décision finale : La migration est validée. Le nouveau système est mis en production.**
