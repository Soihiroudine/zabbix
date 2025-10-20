# zabbix
Tutoriel installation et configuration de Zabbix sur debian 13

## Lien du site Zabbix
Lien vers le site de [Zabbix](https://www.zabbix.com/download?zabbix=7.4&os_distribution=debian&os_version=13&components=server_frontend_agent&db=pgsql&ws=apache)

## Installation de Zabbix Serveur pour Debian

Les packets et ou outils que l'on va utilisé dans notre tutoriel : 
 - Debian 13 pour le système d'exploitation
 - Zabbix
 - Mysql ou MariaDB pour la base de donné
 - Apache2 pour le serveur web

A ne pas oublié, toutes les commandes doivent être executer en mode ***Addministrateur***

### 1. Install Zabbix

```bash
wget https://repo.zabbix.com/zabbix/7.4/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.4+debian13_all.deb
dpkg -i zabbix-release_latest_7.4+debian13_all.deb
apt update
```

### 2. Install Zabbix server, frontend, agent, apache2, mariadb

```bash
apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent apache2 mariadb
```

### 3. Créer une base de donné pour le serveur Zabbix

```bash
mysql
# Mot de passe si demander

mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by '*Votre_mot_de_passe*';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;
```

Sur le serveur Zabbix, importez le schéma et les données initiaux. Vous serez invité à saisir votre nouveau mot de passe.

```bash
zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

Désactivez l'option log_bin_trust_function_creators après l'importation du schéma de base de données.

```bash
 mysql
# Mot de passe si demander

mysql> set global log_bin_trust_function_creators = 0;
mysql> quit;
```

### 4. Le fichier /etc/zabbix/zabbix_server.conf

Mettre le même mot de passe que celuis mis dans la base de donné

```bash
nano /etc/zabbix/zabbix_server.conf

# Chercher *DBPassword* puis mettre votre mot de passe

DBPassword=Votre_mot_de_passe
```

### 5. Activer les port 80 et 443 pour le serveur apache2 (si vous ne les avait pas changer)

```bash
ufw allow 80/tcp
ufw allow 443/tcp
ufw reload
```

### 6. Démarrer les processus du serveur et de l'agent Zabbix

```bash
systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2
```

## Accès à la page web

Allez sur votre navigateur, puis mettre http://<addresse_ip_du serveur>/zabbix

