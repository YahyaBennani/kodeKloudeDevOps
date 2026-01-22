# 📘 Guide Complet : Gestion des Utilisateurs et Groupes sous Linux

## 📝 Sommaire
1. [Création d'utilisateurs](#1-création-dutilisateurs)
2. [Gestion des groupes](#2-gestion-des-groupes)
3. [Fichiers système](#3-fichiers-système)
4. [Comptes système vs interactifs](#4-comptes-système-vs-interactifs)

---

## 1. Création d'utilisateurs

### 🔧 **useradd** (commande bas niveau)

# Syntaxe de base
useradd [options] nom_utilisateur

# Exemple complet
sudo useradd -m -d /home/jim -s /bin/bash -c "Jim Doe" -u 1002 -g users jim

# Options principales :
# -m : Crée le répertoire home
# -d : Spécifie le répertoire home
# -s : Définit le shell (ex: /bin/bash, /sbin/nologin)
# -c : Commentaire/description
# -u : UID (User ID) spécifique
# -g : Groupe primaire
# -G : Groupes secondaires (séparés par des virgules)
# -e : Date d'expiration (YYYY-MM-DD)
# -p : Mot de passe (crypté, déconseillé - utiliser passwd après)


### 🎯 **adduser** (commande interactive - plus conviviale)

# Sous Debian/Ubuntu
sudo adduser nom_utilisateur

# Différences :
# - Pose des questions interactives
# - Crée automatiquement le groupe homonyme
# - Configure le home directory
# - Définit le mot de passe directement


**Comparaison :**

# useradd : Manuel, plus d'options
sudo useradd -m -s /bin/bash alice

# adduser : Automatique, convivial
sudo adduser bob  # Pose des questions


---

## 2. Gestion des groupes

### 📊 **Création et suppression de groupes**

# Créer un groupe
sudo groupadd nom_groupe
sudo groupadd -g 1005 devs  # Avec GID spécifique

# Supprimer un groupe
sudo groupdel nom_groupe

# Voir les groupes d'un utilisateur
groups nom_utilisateur
id nom_utilisateur


### 🔄 **Ajouter/supprimer des membres**

# Ajouter un utilisateur à un groupe
sudo usermod -aG groupe utilisateur  # -a = append (ajouter)

# Exemple : Ajouter Jim aux groupes devs et docker
sudo usermod -aG devs,docker jim

# Supprimer un utilisateur d'un groupe
sudo gpasswd -d utilisateur groupe

# Changer le groupe primaire
sudo usermod -g nouveau_groupe_primair utilisateur


### ⚙️ **gpasswd - Gestion avancée des groupes**

# Définir un mot de passe pour le groupe
sudo gpasswd nom_groupe  # Demande un mot de passe

# Ajouter un administrateur du groupe
sudo gpasswd -A utilisateur nom_groupe

# Ajouter un membre
sudo gpasswd -a utilisateur nom_groupe

# Retirer un membre
sudo gpasswd -d utilisateur nom_groupe


---

## 3. Fichiers système

### 📄 **/etc/passwd** - Informations utilisateurs
**Format :** `nom:x:UID:GID:commentaire:home:shell`


# Exemple :
jim:x:1002:1002:Jim Doe:/home/jim:/bin/bash
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin

# Champs :
# 1. nom      : Nom d'utilisateur (jim)
# 2. x        : Mot de passe (x = stocké dans /etc/shadow)
# 3. UID      : User ID (1002)
# 4. GID      : Group ID primaire (1002)
# 5. GECOS    : Commentaire/description (Jim Doe)
# 6. home     : Répertoire personnel (/home/jim)
# 7. shell    : Shell de connexion (/bin/bash, /sbin/nologin)


### 🔐 **/etc/shadow** - Mots de passe sécurisés
**Format :** `nom:hash:jours:min:max:avert:expire:reserve`


# Exemple :
jim:$6$salt...hash:18647:0:99999:7:::

# Champs principaux :
# 1. nom          : Nom d'utilisateur
# 2. hash         : Mot de passe crypté (ou !/* = désactivé)
# 3. dern_chg     : Jours depuis 1/1/1970 du dernier changement
# 4. min          : Jours minimum entre changements (0 = immédiat)
# 5. max          : Jours maximum avant expiration
# 6. avert        : Jours d'avertissement avant expiration
# 7. expire       : Jours après expiration avant désactivation
# 8. reserve      : Champ réservé


### 👥 **/etc/group** - Définition des groupes
**Format :** `nom_groupe:x:GID:membres`


# Exemple :
devs:x:1005:jim,alice,bob
docker:x:131:jim

# Champs :
# 1. nom_groupe : Nom du groupe (devs)
# 2. x          : Mot de passe du groupe (x = dans gshadow)
# 3. GID        : Group ID (1005)
# 4. membres    : Liste des membres (séparés par virgules)


### 🛡️ **/etc/gshadow** - Sécurité des groupes
**Format :** `nom_groupe:hash:admins:membres`


# Exemple :
devs:$6$salt...:admin1,admin2:jim,alice,bob

# Champs :
# 1. nom_groupe : Nom du groupe
# 2. hash       : Mot de passe crypté du groupe
# 3. admins     : Administrateurs du groupe
# 4. membres    : Membres du groupe


---

## 4. Comptes système vs interactifs

### 🚫 **/sbin/nologin** - Comptes non-interactifs

# Créer un compte système (service account)
sudo useradd -r -s /sbin/nologin -M mon_service

# Options :
# -r : Crée un compte système (UID < 1000)
# -s /sbin/nologin : Interdit la connexion interactive
# -M : Ne crée pas le répertoire home

# Vérifier :
cat /etc/passwd | grep nologin
# Exemples courants : mysql, nginx, postfix, sshd


**Pourquoi utiliser nologin ?**
- ✅ **Sécurité** : Empêche l'accès shell
- ✅ **Propriété** : Fichiers/services peuvent appartenir au compte
- ✅ **Isolation** : Limite les dégâts en cas de compromission
- ❌ **Pas de connexion** : SSH, su, login impossible

### 🔄 **su - Changer d'utilisateur**

# Basculer vers un autre utilisateur
su - utilisateur  # Avec environnement complet
su utilisateur    # Environnement partiel

# Devenir root
su -              # Avec environnement root
sudo -i           # Alternative avec sudo

# Options :
# -c : Exécuter une commande spécifique
su -c "whoami" utilisateur

# Avec nologin :
su - apache
# Résultat : "This account is currently not available"


---

## 🎯 **Récapitulatif des commandes essentielles**

| Action | Commande | Exemple |
|--------|----------|---------|
| Créer utilisateur | `useradd` ou `adduser` | `sudo useradd -m bob` |
| Modifier utilisateur | `usermod` | `sudo usermod -aG sudo bob` |
| Supprimer utilisateur | `userdel` | `sudo userdel -r bob` |
| Changer mot de passe | `passwd` | `sudo passwd bob` |
| Créer groupe | `groupadd` | `sudo groupadd devs` |
| Modifier groupe | `groupmod` | `sudo groupmod -n newname oldname` |
| Gérer membres | `gpasswd` | `sudo gpasswd -a bob devs` |
| Voir informations | `id`, `groups` | `id bob` |
| Changer d'identité | `su` | `su - bob` |

---

## 📊 **Exemple complet de workflow**


# 1. Créer un groupe de développeurs
sudo groupadd -g 2000 developers

# 2. Créer un utilisateur développeur
sudo useradd -m -d /home/alice -s /bin/bash -c "Alice Dev" -u 2001 -g developers alice
sudo passwd alice

# 3. Ajouter aux groupes secondaires
sudo usermod -aG sudo,docker,video alice

# 4. Créer un compte service pour une app
sudo useradd -r -s /sbin/nologin -M monapp
sudo chown monapp:monapp /opt/monapp/*

# 5. Vérifier
id alice
groups alice
cat /etc/passwd | grep -E "(alice|monapp)"


---

## ⚠️ **Bonnes pratiques**

1. **UID/GID** : Utiliser des plages cohérentes
   - 0-999 : Comptes système
   - 1000- : Utilisateurs normaux

2. **Sécurité** :
   - Toujours utiliser `/sbin/nologin` pour les services
   - Limiter l'accès `sudo` au minimum
   - Changer régulièrement les mots de passe

3. **Documentation** :
   - Utiliser le champ commentaire (GECOS)
   - Maintenir des listes à jour des groupes

4. **Suppression** :
 
   # Conserver les fichiers
   sudo userdel utilisateur
   
   # Supprimer complètement (home + spool)
   sudo userdel -r utilisateur


---

## 🔗 **Ressources complémentaires**
# Documentation
man useradd
man usermod
man groupadd
man passwd
man shadow

# Fichiers à consulter
/etc/login.defs    # Paramètres par défaut
/etc/default/useradd # Configuration useradd
/etc/skel/         # Squelette du home directory


## day1 write up
# Creating Linux Users with Non-Interactive Shells
- ssh banner@172.16.238.12
- sudo su
- cat /etc/passwd | grep jim
- adduser jim -s /sbin/nologin
- cat /etc/passwd | grep nologin
## day2 write up
# Creating Linux Users with Temporary User Setup with Expiry
- ssh steve@stapp02.stratos.xfusioncorp.com
- sudo su -
- useradd kareem -e 2027-04-15
# ssh
# 📘 Cours Complet SSH (Secure Shell)

## 🎯 Objectifs du cours

Ce cours a pour but de vous donner **une maîtrise complète de SSH** :

* Comprendre le fonctionnement interne de SSH
* Maîtriser **toutes les options importantes** côté client et serveur
* Mettre en place un **hardening SSH professionnel** (niveau DevSecOps / CEH / Admin Sys)
* Appliquer les **bonnes pratiques sécurité (CIS, ANSSI, NIST)**

---

## 1️⃣ Introduction à SSH

### 🔐 Qu’est-ce que SSH ?

SSH (Secure Shell) est un protocole réseau sécurisé permettant :

* Connexion distante sécurisée
* Exécution de commandes à distance
* Transfert de fichiers sécurisé
* Tunneling et port forwarding

➡️ Il remplace **Telnet**, **rlogin**, **FTP** (non sécurisés).

### 📡 Ports et protocoles

* Port par défaut : **22/TCP**
* Basé sur TCP
* Chiffrement asymétrique + symétrique

---

## 2️⃣ Architecture SSH

### 🔄 Modèle Client / Serveur

* **Client SSH** : `ssh`, `scp`, `sftp`
* **Serveur SSH** : `sshd`

### 📁 Fichiers importants

| Fichier                | Rôle                             |
| ---------------------- | -------------------------------- |
| /etc/ssh/sshd_config   | Configuration serveur            |
| /etc/ssh/ssh_config    | Configuration client globale     |
| ~/.ssh/config          | Configuration client utilisateur |
| ~/.ssh/authorized_keys | Clés autorisées                  |
| ~/.ssh/known_hosts     | Empreintes serveurs              |

---
clien c a d moi quand je tent a se connecter a une autre machine 
server lorsque les autres essaye de se connecter a ma machine
## 3️⃣ Mécanismes de chiffrement SSH

### 🔑 Types de chiffrement

#### 1. Chiffrement asymétrique

* RSA
* ECDSA
* Ed25519 (🔥 recommandé)

#### 2. Chiffrement symétrique (session)

* AES
* ChaCha20

#### 3. Intégrité

* HMAC-SHA2

---

## 4️⃣ Authentification SSH

### 🔑 Authentification par mot de passe

```text
PasswordAuthentication yes
```

❌ Vulnérable au brute-force

### 🔐 Authentification par clé SSH (recommandée)

```bash
ssh-keygen -t ed25519
ssh-copy-id user@server
```

```text
PubkeyAuthentication yes
```

---

## 5️⃣ Options principales de sshd_config

### 🔒 Accès root

```text
PermitRootLogin no
```

### 🔑 Méthodes d’authentification

```text
PasswordAuthentication no
PubkeyAuthentication yes
KbdInteractiveAuthentication no
ChallengeResponseAuthentication no
```

### 👥 Contrôle d’accès utilisateurs

```text
AllowUsers user1 user2
AllowGroups sshusers
DenyUsers test guest
```

### 🕒 Timeout et sessions

```text
LoginGraceTime 30
ClientAliveInterval 300
ClientAliveCountMax 2
MaxSessions 2
MaxAuthTries 3
```

---

## 6️⃣ Hardening SSH (sécurisation avancée)

### 🛡️ Désactiver protocoles faibles

```text
Protocol 2
```

### 🔐 Chiffres sécurisés

```text
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com
MACs hmac-sha2-512-etm@openssh.com
KexAlgorithms curve25519-sha256
```

### 🔎 Bannière légale

```text
Banner /etc/issue.net
```

### 🧱 Limiter forwarding

```text
AllowTcpForwarding no
X11Forwarding no
PermitTunnel no
```

---

## 7️⃣ SSH Client Hardening

### 📄 ~/.ssh/config

```text
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 2
    HashKnownHosts yes
    IdentitiesOnly yes
```

---

## 8️⃣ Port Forwarding SSH

### 🔁 Local forwarding

```bash
ssh -L 8080:localhost:80 user@server
```

### 🔁 Remote forwarding

```bash
ssh -R 9000:localhost:9000 user@server
```

### 🔁 Dynamic (SOCKS proxy)

```bash
ssh -D 1080 user@server
```

---

## 9️⃣ SFTP & SCP

### 📂 SCP

```bash
scp file.txt user@server:/tmp
```

### 📂 SFTP sécurisé

```bash
Subsystem sftp internal-sftp
```

---

## 🔟 Journaux & audit SSH

### 📜 Logs

```bash
/var/log/auth.log
/var/log/secure
```

### 🔍 Augmenter la verbosité

```text
LogLevel VERBOSE
```

---

## 1️⃣1️⃣ Protection contre attaques

### 🔨 Fail2Ban

* Bloque brute-force SSH

### 🔐 Changer le port SSH

```text
Port 2222
```

### 🧱 Firewall

```bash
ufw allow 2222/tcp
```

---

## 1️⃣2️⃣ Conformité Sécurité

### 📘 CIS Benchmarks

* Disable root login
* Disable password auth
* Strong crypto only
* Logging enabled

### 📘 ANSSI / NIST

* MFA recommandé
* Bastion SSH

---

## 1️⃣3️⃣ Checklist SSH Hardening (audit-ready)

* [ ] PermitRootLogin no
* [ ] PasswordAuthentication no
* [ ] Clés Ed25519
* [ ] Fail2Ban actif
* [ ] Port non standard
* [ ] Logs activés
* [ ] Firewall restrictif

---

## 📌 Conclusion

SSH est **un composant critique de la sécurité système**. Un mauvais durcissement = accès root distant.

➡️ En cybersécurité, **SSH mal configuré = compromission totale**.

---


## day3 write up
# disable direct SSH root login
- sudo nano /etc/ssh/sshd_config
- #PermitRootLogin yes --> PermitRootLogin no
- sudo sshd -t (test) si aucune sortie --> OK
- if not --> sudo systemctl restart ssh
