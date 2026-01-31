## day8 ansible
````markdown
# Cheatsheet : Installer Ansible 4.10.0 globalement avec pip3

---

## 1️⃣ Vérifier Python et pip3
```bash
python3 --version     # Version Python
pip3 --version        # Version pip3
````

## 2️⃣ Installer Python3 + pip3 (si nécessaire)

**Ubuntu/Debian :**

```bash
sudo apt update
sudo apt install -y python3 python3-pip
```

**RHEL/CentOS/Rocky/AlmaLinux :**

```bash
sudo dnf install -y python3 python3-pip
```

## 3️⃣ Désinstaller anciennes versions d’Ansible

```bash
sudo pip3 uninstall ansible ansible-core -y
```

## 4️⃣ Installer Ansible 4.10.0 globalement

```bash
sudo pip3 install "ansible==4.10.0"
```

## 5️⃣ Vérifier le binaire et version

```bash
which ansible          # Chemin du binaire (ex: /usr/local/bin/ansible)
ansible --version      # Vérifie version 4.10.0
```

## 6️⃣ Assurer accès global pour tous les utilisateurs

* Vérifier PATH global :

```bash
echo $PATH
```

* Ajouter `/usr/local/bin` si absent :

```bash
echo 'export PATH=$PATH:/usr/local/bin' | sudo tee -a /etc/profile
source /etc/profile
```

## 7️⃣ Test multi-utilisateur

```bash
su - <autre_utilisateur>
ansible --version
```

---

## ✅ Résumé rapide des commandes

| Commande                                                  | Rôle                                 |
| --------------------------------------------------------- | ------------------------------------ |
| `python3 --version`                                       | Vérifie Python 3                     |
| `pip3 --version`                                          | Vérifie pip3                         |
| `sudo apt install python3 python3-pip`                    | Installer Python/pip sur Ubuntu      |
| `sudo dnf install python3 python3-pip`                    | Installer Python/pip sur RHEL/CentOS |
| `sudo pip3 uninstall ansible ansible-core -y`             | Supprime anciennes versions          |
| `sudo pip3 install "ansible==4.10.0"`                     | Installe Ansible 4.10.0 globalement  |
| `which ansible`                                           | Chemin du binaire                    |
| `ansible --version`                                       | Vérifie version et dépendances       |
| `echo 'export PATH=$PATH:/usr/local/bin' >> /etc/profile` | Assure chemin global                 |
| `su - <user>`                                             | Test multi-utilisateur               |

---

💡 **Tips :**

* Installer via pip3 permet de choisir la version exacte.
* `/usr/local/bin` doit être dans le PATH pour tous les utilisateurs.
* Seul le **jump host / contrôle node** doit avoir Ansible.
