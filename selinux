# SELinux — Résumé explicatif détaillé

## 1. Qu’est‑ce que SELinux ?

**SELinux (Security‑Enhanced Linux)** est un mécanisme de sécurité obligatoire (**MAC – Mandatory Access Control**) intégré au noyau Linux. Il renforce la sécurité du système en imposant des règles strictes qui contrôlent **qui peut faire quoi, sur quoi**, indépendamment des permissions classiques Linux.

> Objectif principal : **limiter l’impact d’une compromission** (ex. un service piraté ne peut accéder qu’à ce qui lui est explicitement autorisé).

---

## 2. DAC vs MAC (concept clé)

### DAC – Discretionary Access Control (classique Linux)

* Basé sur : **utilisateur / groupe / permissions (rwx)**
* Le propriétaire peut modifier les permissions
* Exemple : `chmod 777 fichier`

### MAC – Mandatory Access Control (SELinux)

* Les règles sont **imposées par le système**
* Même `root` est limité
* Les processus et ressources sont contrôlés par des **politiques de sécurité**

👉 **SELinux = couche de sécurité supplémentaire au‑dessus des permissions Linux**.

---

## 3. Principe fondamental de SELinux

SELinux fonctionne selon le principe :

> **Tout est interdit par défaut, sauf ce qui est explicitement autorisé**

Chaque action est évaluée selon :

* Le **contexte de sécurité** du processus
* Le **contexte de sécurité** de la ressource (fichier, port, socket…)
* Les **règles de la politique SELinux**

---

## 4. Les contextes de sécurité SELinux

Un contexte SELinux est composé de **4 champs** :

```text
user:role:type:level
```

### Exemple

```text
system_u:system_r:httpd_t:s0
```

### Signification

| Champ | Rôle                                      |
| ----- | ----------------------------------------- |
| user  | Utilisateur SELinux (≠ utilisateur Linux) |
| role  | Rôle SELinux (RBAC)                       |
| type  | **Le plus important** (Type Enforcement)  |
| level | Niveau MLS/MCS (sensibilité)              |

📌 **90% des règles SELinux reposent sur le champ `type`**.

---

## 5. Type Enforcement (TE) – cœur de SELinux

SELinux applique des règles du type :

> Un **processus de type A** peut accéder à une **ressource de type B** avec des **permissions C**

### Exemple

```text
httpd_t  →  httpd_sys_content_t  (read)
```

➡ Le processus Apache (`httpd_t`) peut lire les fichiers web (`httpd_sys_content_t`).

---

## 6. Modes de fonctionnement de SELinux

| Mode           | Description                                      |
| -------------- | ------------------------------------------------ |
| **Enforcing**  | Applique les règles + bloque les accès interdits |
| **Permissive** | N’applique pas, mais **loggue** les violations   |
| **Disabled**   | SELinux complètement désactivé                   |

### Commandes utiles

```bash
getenforce
setenforce 0   # permissive
setenforce 1   # enforcing
```

⚠️ Le mode `disabled` nécessite un **redémarrage**.

---

## 7. Politiques SELinux

Les politiques définissent les règles de sécurité.

### Types principaux

| Politique    | Description                                             |
| ------------ | ------------------------------------------------------- |
| **targeted** | Seuls les services critiques sont protégés (par défaut) |
| **mls**      | Sécurité multiniveau (militaire)                        |
| **minimum**  | Version allégée de targeted                             |

### Fichier de configuration

```bash
/etc/selinux/config
```

```ini
SELINUX=enforcing
SELINUXTYPE=targeted
```

---

## 8. Gestion des fichiers et labels

Chaque fichier possède un **label SELinux**.

### Afficher les contextes

```bash
ls -Z
ps -Z
```

### Restaurer les contextes par défaut

```bash
restorecon -Rv /var/www/html
```

### Modifier un contexte (bonne pratique)

```bash
semanage fcontext -a -t httpd_sys_content_t "/data/web(/.*)?"
restorecon -Rv /data/web
```

❌ Éviter `chcon` en production (non persistant).

---

## 9. Ports et services SELinux

SELinux contrôle aussi les **ports réseau**.

### Lister les ports autorisés pour HTTP

```bash
semanage port -l | grep http
```

### Autoriser Apache sur un nouveau port

```bash
semanage port -a -t http_port_t -p tcp 8081
```

---

## 10. Boolean SELinux

Les **booleans** activent/désactivent des comportements sans modifier la politique.

### Exemples

```bash
getsebool -a
setsebool -P httpd_can_network_connect on
```

| Boolean                   | Effet                             |
| ------------------------- | --------------------------------- |
| httpd_can_network_connect | Apache peut sortir vers le réseau |
| ftpd_full_access          | Accès complet FTP                 |

---

## 11. Logs et débogage SELinux

### Fichiers de logs

```bash
/var/log/audit/audit.log
```

### Outils essentiels

```bash
audit2why < audit.log
audit2allow -M mypolicy < audit.log
```

⚠️ Ne jamais appliquer aveuglément une règle générée.

---

## 12. Cas réel d’erreur SELinux

### Problème

Apache retourne **403 Forbidden** alors que les permissions Linux sont correctes.

### Diagnostic

```bash
ls -Z /var/www/html
```

### Solution

```bash
restorecon -Rv /var/www/html
setsebool -P httpd_can_network_connect on
```

---

## 13. Bonnes pratiques

✅ Garder SELinux **enforcing**
✅ Utiliser **semanage** plutôt que `chcon`
✅ Lire les logs avant toute action
✅ Préférer les **booleans** aux règles custom
❌ Désactiver SELinux en production

---

## 14. Pourquoi SELinux est crucial en cybersécurité

* Réduit drastiquement l’impact des exploits
* Empêche l’escalade latérale
* Complément idéal au hardening Linux
* Standard en environnements **Red Hat, CentOS, Rocky, AlmaLinux**

---

## 15. Résumé ultra‑court

> SELinux applique une sécurité **obligatoire**, basée sur des **politiques**, des **types** et des **labels**, pour contrôler finement chaque action sur un système Linux.

---

📌 **SELinux n’est pas un obstacle, c’est un garde‑fou.**
