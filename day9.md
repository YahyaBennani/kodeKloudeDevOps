## 📂 Dossiers principaux de MariaDB

| Dossier                                              | Propriétaire / Permission typique | Rôle / Description                                                                                                                 |
| ---------------------------------------------------- | --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `/etc/my.cnf` ou `/etc/mysql/`                       | root:root                         | Fichier de configuration global de MariaDB. Peut contenir des `include` vers d’autres fichiers comme `/etc/mysql/mariadb.conf.d/`. |
| `/var/lib/mysql/`                                    | mysql:mysql                       | **Dossier des données** : contient les bases de données, tables, index, journaux InnoDB.                                           |
| `/var/lib/mysql/mysql.sock`                          | mysql:mysql                       | Fichier socket pour les connexions locales à MariaDB.                                                                              |
| `/var/log/mariadb/` ou `/var/log/mysql/`             | mysql:mysql                       | Logs du serveur : démarrage/arrêt, erreurs, alertes.                                                                               |
| `/run/mariadb/`                                      | mysql:mysql                       | Dossier temporaire pour le **PID** du serveur (`mariadb.pid`) et parfois pour les fichiers de lock.                                |
| `/usr/libexec/mariadbd`                              | root:root                         | Binaire principal du serveur MariaDB.                                                                                              |
| `/usr/share/mysql/`                                  | root:root                         | Scripts et fichiers de support MariaDB, comme les fichiers SQL initiaux.                                                           |
| `/var/run/mysqld/` (sur certaines distributions)     | mysql:mysql                       | Ancien emplacement pour le PID et socket.                                                                                          |
| `/tmp/` ou `/var/tmp/`                               | dépend du système                 | Fichiers temporaires, notamment InnoDB crée `ibtmp1` ici si `tmpdir` est défini.                                                   |
| `/etc/mysql/conf.d/` ou `/etc/mysql/mariadb.conf.d/` | root:root                         | Fichiers de configuration additionnels (par service, réplication, etc.)                                                            |

---

## 📄 Fichiers importants

| Fichier          | Emplacement                          | Rôle                                                                   |
| ---------------- | ------------------------------------ | ---------------------------------------------------------------------- |
| `my.cnf`         | `/etc/my.cnf` ou `/etc/mysql/my.cnf` | Configuration générale du serveur (port, chemins, buffer, logs, etc.)  |
| `mariadb.pid`    | `/run/mariadb/mariadb.pid`           | Contient l’**ID du processus** du serveur MariaDB en cours d’exécution |
| `mysql.sock`     | `/var/lib/mysql/mysql.sock`          | Fichier **socket UNIX** pour les connexions locales                    |
| `ibdata1`        | `/var/lib/mysql/`                    | Fichier principal de stockage InnoDB (tables, index, métadonnées)      |
| `ibtmp1`         | `/var/lib/mysql/` (temp)             | Tablespace temporaire InnoDB                                           |
| `ib_buffer_pool` | `/var/lib/mysql/`                    | Sauvegarde du buffer pool InnoDB pour accélérer le redémarrage         |
| `*.err`          | `/var/log/mariadb/`                  | Fichier de logs d’erreurs et de démarrage                              |
| `*.log`          | `/var/log/mariadb/`                  | Journaux généraux ou binlogs (si activés)                              |

---

## 🔹 Récapitulatif des rôles

1. **Configuration** → `/etc/my.cnf`, `/etc/mysql/mariadb.conf.d/`
2. **Binaire serveur** → `/usr/libexec/mariadbd`
3. **Données** → `/var/lib/mysql/`
4. **Socket et PID** → `/var/lib/mysql/mysql.sock`, `/run/mariadb/mariadb.pid`
5. **Logs** → `/var/log/mariadb/`
6. **Temporaire / tablespace** → `/tmp/`, `ibtmp1`

---

💡 **Astuce** : pour voir tous les fichiers ouverts par MariaDB quand il tourne :

```bash
sudo lsof -u mysql
```

Ça te liste **tous les fichiers et sockets utilisés en temps réel**, pratique pour le debugging.

---

### 🗂️ Diagramme de MariaDB (Linux)

```
MariaDB Server
│
├─ /etc/my.cnf (root:root)
│    └─ Configuration globale du serveur
│
├─ /etc/mysql/mariadb.conf.d/ (root:root)
│    └─ Configurations supplémentaires (réplication, performance, etc.)
│
├─ /usr/libexec/mariadbd (root:root)
│    └─ Binaire principal du serveur MariaDB
│
├─ /var/lib/mysql/ (mysql:mysql)
│    ├─ Base de données et tables
│    ├─ ibdata1 → Tablespace InnoDB principal
│    ├─ ibtmp1 → Tablespace temporaire InnoDB
│    ├─ ib_buffer_pool → Sauvegarde du buffer pool
│    └─ mysql.sock → Socket UNIX pour connexions locales
│
├─ /run/mariadb/ (mysql:mysql)
│    └─ mariadb.pid → PID du serveur en cours d’exécution
│
├─ /var/log/mariadb/ (mysql:mysql)
│    ├─ mariadb.log → Log principal (démarrage, erreurs)
│    ├─ *.err → Log d’erreurs
│    └─ *.log → Journaux généraux ou binlogs (si activés)
│
└─ /tmp/ ou /var/tmp/ (root:root)
     └─ Fichiers temporaires, tables temporaires InnoDB
```

---

💡 **Résumé rapide du flux :**

* **Binaire** (`/usr/libexec/mariadbd`) lit la **config** (`/etc/my.cnf`) → démarre → utilise **/var/lib/mysql/** pour données → crée **socket et PID** → écrit les **logs** → peut utiliser `/tmp` pour tables temporaires.

---

## day9 writeup
- ssh peter@172.16.239.10
- sudo systemctl status mariadb
- sudo cat /var/log/mariadb/mariadb.log
- ls -ld /run/mariadb
- sudo chown mysql:mysql /run/mariadb
- sudo systemctl restart mariadb
