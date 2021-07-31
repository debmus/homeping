# Home Ping

Ping des hosts en Vuejs

## Fonctionement
- Le fichier `hosts` contient les IP à ping, vous devez respecter le format "ip hostname"
- Une Tâche cron éxécute toutes les minutes un ping basé sur le fichier `hosts`

### Arborescence
```bash
.
├── app.js
├── favicon.ico
├── getIP.php		# Scripts php qui retourne l'adresse IP local du client
├── hosts		# Fichier à éditer contenant les IP à ping
├── index.html
├── LICENCE	
├── ping.sh		# Script qui éxécuter un ping en se basant sur le fichier 'hosts'
├── README.md
├── result.json		# Résultat du ping au format json extrait par le script 'ping.sh'
└── VERSION
```

## Paramétrage Linux 
1. Copier le dossier `homeping` dans `/var/www/homeping` et appliquer au dossier les droits de l'utilisateur courant 
```bash
git clone https://github.com/debmus/homeping.git
sudo chown -R $USER: /var/www/homeping
```

2. Rendre le script `ping.sh` éxécutable 
```bash
sudo chmod -x /var/www/homeping/ping.sh
```

3. Ajouter dans cron (avec la commande `crontab -e`) la ligne suivante pour éxécuter le script `ping.sh` toutes les minutes
```bash
* * * * * /var/www/homeping/ping.sh
```

## Paramétrage de Apache2
1.	Installer apache2 et php
```bash
sudo apt install apache2 php
```

2.	Activer le module Headers de Apache2
```bash
sudo a2enmod headers
sudo systemctl restart apache2
```

3.	Copier le contenu du virtualhost dans `/etc/apache2/sites-available/homeping.conf`
```bash
<VirtualHost *:9292>

	DocumentRoot /var/www/homeping
	DirectoryIndex index.html

	<Directory /var/www/homeping>
		Header set Access-Control-Allow-Origin "*"
		Header set Access-Control-Allow-Headers "Content-Type"
		Options FollowSymLinks
		AllowOverride All
		Require all granted
	</Directory>

	ErrorLog /var/log/apache2/error.log
	CustomLog /var/log/apache2/access.log common

</VirtualHost>
```

4.	Ajouter le port **9292** dans le fichier `/etc/apache2/ports.conf`
```bash
Listen 9292
```

5.	Relancer Apche2 et se connecter à l'URL `http://<ip>:9292`
```bash
sudo systemctl restart apache2
```