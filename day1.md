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


day1 write up
Creating Linux Users with Non-Interactive Shells
ssh banner@172.16.238.12
sudo su
cat /etc/passwd | grep jim
adduser jim -s /sbin/nologin
cat /etc/passwd | grep nologin
