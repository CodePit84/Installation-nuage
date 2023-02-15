D'abord copié sa clé SSH :

![clé_ssh](https://github.com/CodePit84/Installation-nuage/blob/main/cle_ssh.jpg)

Créer une instance sur nuage (ne pas tenir compte du nom de l'instance dans les screensshot suivants car il y a eu un bug à la création de la 1ère instance)

Nom :

Système : Debian

![création_nuage](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-14%2013-50-07.png)

Attention bien mettre "Associer une IP et ouvrir l'accès SSH" au niveau de l'IP publique !

Et coller votre clé SSH en cliquant sur "Importer une clé" (lui donné un nom ou laissé l'email par defaut)

Et créer l'instance !

Assurer vous que l'instance soit en "vert"

![instance](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-14%2013-57-24.png)

Puis Aller dans "Groupes de sécurité" (en bas) et cliquer sur "Assigner des instances" de "Autoriser tout le trafic depuis Internet"

![instance](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-14%2013-58-07.png)

Sur votre ordinateur, vous devez lié votre clé SSH à votre instance.

Dans votre terminal, au niveau de votre dossier .ssh
Tapez : 
```nano config```

![nano_config](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-15%2010-27-34.png)

Editez le fichier `~/.ssh/config pour lui ajouter ceci :

```bash
# On remplace les xxx par l'adresse ip de votre machine virtuelle
Host xxx.xxx.xxx.xxx
  AddKeysToAgent yes
  # UseKeychain no
  # On remplace maclé par le nom de votre clé privée
  IdentityFile ~/.ssh/maclé

```

![config](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-14%2014-20-04.png)

donc votre ip publique après Host et vous commenté UseKeychain no !

Une fois votre Machine Virtuelle crée, vous pouvez y accéder en faisant la commande `ssh debian@xxx.xxx.xxx.xxx` les xxx étant l'adresse ip publique de votre machine virtuelle.

Donc sur votre terminal, vous tapez :
```ssh debian@xxx.xxx.xxx.xxx```
remplacer les xxx par votre ip publique !

![ssh_debian](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-15%2010-35-49.png)
Répondre yes à la question "Are you sure you want to continue connecting (yes/no/[fingerprint])?"

Désormais pour vous connecter utiliser la commande ```ssh debian@xxx.xxx.xxx.xxx``` pour vous connectez au terminal de votre serveur distant.

/// Copié/collé de Guillaume à partir de là : /!\ Mais Ajout de l'installation de MYSQL à la fin de l'installation de base !! ///

### Installation de base

On se connecte sur la droplet en SSH.
Utilisez sudo si vous n'êtes pas root :

```BASH
# On mets à jour le système
sudo apt update && sudo apt upgrade -y 

# On installe le serveur web
sudo apt-get install -y nginx

# On installe les utilitaires necessaires
sudo apt-get install -y zip git libzip-dev
sudo apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2   

# On configure les locales
nano /etc/locale.gen
# On décommente fr_FR.UTF-8 UTF-8 et en_US.UTF-8 UTF-8
sudo locale-gen

# On rajoute un repository qui va permettre d'installer la dernière version de PHP
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/sury-php.list
wget -qO - https://packages.sury.org/php/apt.gpg | sudo gpg --no-default-keyring --keyring gnupg-ring:/etc/apt/trusted.gpg.d/debian-php-8.gpg --import
sudo chmod 644 /etc/apt/trusted.gpg.d/debian-php-8.gpg

# Après un ajout de repository, on refait une update
sudo apt update && sudo apt upgrade -y

# On installe PHP
sudo apt-get install -y php8.1

# On installe les extensions dont on a besoin
sudo apt-get install -y php8.1-fpm php8.1-SimpleXML php8.1-mbstring php8.1-intl php8.1-pgsql

# On installe composer
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '55ce33d7678c5a611085589f1f3ddf8b3c52d662cd01d4ba75c0ee0459970c2200a51f492d557530c71c15d8dba01eae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
sudo mv composer.phar /usr/local/bin/composer

# On installe la CLI Symfony
curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.deb.sh' | sudo -E bash
sudo apt-get install -y symfony-cli

# On configure GIT
git config --global user.email "you@example.com"
git config --global user.name "Votre Nom"

# On install PostgreSQL
sudo apt-get install -y postgresql postgresql-contrib

# On installe Node et NPM
curl -sL https://deb.nodesource.com/setup_16.x -o nodesource_setup.sh 
sudo bash nodesource_setup.sh 
sudo apt install nodejs

# On installe MySQL
wget https://dev.mysql.com/get/mysql-apt-config_0.8.22-1_all.deb
sudo apt install ./mysql-apt-config_0.8.22-1_all.deb
sudo apt update
sudo apt install mysql-server

```

### Activation du firewall

Avant de pouvoir avoir accès à votre serveur depuis un navigateur web, vous devez dire au firewall de Nua.ge d'autoriser le trafic web.

Pour celà, il faut cliquer sur "groupes de sécurité" en bas de page du dashbord de Nua.ge.

Puis `ajouter des instance` sur la ligne `Autoriser tout le trafic web depuis Internet`

Et là vous cliquez sur `Assigner` en face de votre machine virtuelle.

Ouvrez un navigateur et tapez l'adresse ip de votre machine virtuelle dans le navigateur et vous devez avoir une page qui s'affiche (Apache2 Debian Default Page).

### Installer un projet Symfony

Pour installer un site Symfony sur le serveur :

```BASH

# On crée un projet Symfony 
cd /var/www/html

# On crée un projet comme dsur notre propre machine
symfony new monprojettest --webapp

```

Il faudra ensuite configurer le serveur web. Nous utilisons Nginx.

Nginx possède deux dossiers :

- `/etc/nginx/sites-available/` qui correspond aux sites disponibles
- `/etc/nginx/sites-enabled/` qui correspond aux sites activés

Sites disponibles : la configuration est là, mais le site n'est pas en ligne
Sites activés : sites en ligne

Dans le premier dossier, on va créer un fichier par site que l'on veut rendre disponible.
Dans le second, on va créer des lien symboliques (avec `ln -s`) vers les sites que l'on veut mettre en ligne.

Il existe une configuration officielle pour configurer un site Symfony sur Nginx : [https://symfony.com/doc/current/setup/web_server_configuration.html#nginx](https://symfony.com/doc/current/setup/web_server_configuration.html#nginx)

Nous allons donc créer un fichier dans le dossier `sites-available` : `nano /etc/nginx/sites-availlable/monprojettest` dans lequel vous allez collez la [configuration officielle](https://symfony.com/doc/current/setup/web_server_configuration.html#nginx).

Nous allons changer trois choses :

- le chemin vers le dossier de notre site : attribut `root` dans le fichier config, dans lequel nous allons mettre `root /var/www/html/monprojettest/public`
Dans Symfony, c'est bien le projet public qu'il faut pointer
- Nous allons commenter la ligne `server_name` car nous n'avons pas encore de nom de domaine configuré.
- Nous allons mettre le bon chemin vers PHP : attribut `fastcgi_pass` dont nous allons changer la version de 7.4 à 8.1.

Celà nous donne un fichier `/etc/nginx/sites-available/monprojettest` comme ceci :

```BASH

server {
    # server_name domain.tld www.domain.tld;
    root /var/www/html/monprojettest/public;

    location / {
        # try to serve file directly, fallback to index.php
        try_files $uri /index.php$is_args$args;
    }

    # optionally disable falling back to PHP script for the asset directories;
    # nginx will return a 404 error when files are not found instead of passing the
    # request to Symfony (improves performance but Symfony's 404 page is not displayed)
    # location /bundles {
    #     try_files $uri =404;
    # }

    location ~ ^/index\.php(/|$) {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;

        # optionally set the value of the environment variables used in the application
        # fastcgi_param APP_ENV prod;
        # fastcgi_param APP_SECRET <app-secret-id>;
        # fastcgi_param DATABASE_URL "mysql://db_user:db_pass@host:3306/db_name";

        # When you are using symlinks to link the document root to the
        # current version of your application, you should pass the real
        # application path instead of the path to the symlink to PHP
        # FPM.
        # Otherwise, PHP's OPcache may not properly detect changes to
        # your PHP files (see https://github.com/zendtech/ZendOptimizerPlus/issues/126
        # for more information).
        # Caveat: When PHP-FPM is hosted on a different machine from nginx
        #         $realpath_root may not resolve as you expect! In this case try using
        #         $document_root instead.
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
        # Prevents URIs that include the front controller. This will 404:
        # http://domain.tld/index.php/some-path
        # Remove the internal directive to allow URIs like this
        internal;
    }

    # return 404 for all other php files not matching the front controller
    # this prevents access to other php files you don't want to be accessible.
    location ~ \.php$ {
        return 404;
    }

    error_log /var/log/nginx/project_error.log;
    access_log /var/log/nginx/project_access.log;
}

```

Ensuite, nous supprimons le lien symbolique dans `site-enabled`et nous en créons un vers notre fichier `monprojettest`

```BASH

cd /etc/nginx/sites-enabled/
rm default
ln -s /etc/nginx/sites-available/monprojettest
```

Il ne nous reste plus qu'a redémarrer nginx : `/etc/init.d/nginx restart` et d'accéder à notre site en tapant l'adresse IP de la droplet dans un navigateur.

### Quelques commandes de base

- Démarrer le serveur web Nginx : `/etc/init.d/nginx start` ou `systemctl start nginx`
- Stopper le serveur web Nginx : `/etc/init.d/nginx stop` ou `systemctl stop nginx`
- Redémarrer le serveur web Nginx : `/etc/init.d/nginx restart` ou `systemctl restart nginx`
- Savoir si Nginx tourne ou pas : `systemctl status nginx`
- Démarrer la base de données PostgreSQL : `pg_ctlcluster 13 main start` ou `systemctl start postgresql`
- Stopper la base de données PostgreSQL : `pg_ctlcluster 13 main stop` ou `systemctl stop postgresql`
- Redémarrer la base de données PostgreSQL : `pg_ctlcluster 13 main restart` ou `systemctl restart postgresql`
- Savoir si PostgreSQL tourne ou pas : `systemctl status postgresql`

### On vérifies les différentes installations en affichant les versions

- Nginx : `nginx -v`
- PostgreSQL : `sudo -u postgres psql -c "SELECT version();"`
- PHP : `php -v`
- Symfony : `symfony -V`
- Composer : `composer -V`
- Node.js : `node -v`
- NPM : `npm -v`

### Créer un utilisateur et une base de données pour PostgreSQL

- On crée l'utilisateur : `sudo su - postgres -c "createuser monuser"`
- On crée la base : `sudo su - postgres -c "createdb mabase"`
- On se connecte à PostgreSQL pour la suite : `sudo -u postgres psql`
- On donne les droits sur la base à l'utilisateur : `GRANT ALL PRIVILEGES ON DATABASE mabase TO monuser;`
- On définie le mot de passe pour l'utilisateur : `alter user monuser password 'monpassword';`
- Pour quitter psql : `\q`

### Autoriser l'accès à la base de données à l'extérieur du serveur

Par défaut, seules les applications tournant sur le même serveur que la base de données peuvent y accéder.
C'est une bonne pratique, permettant de limiter les potentiels piratages.
Pour des raisons pédagogiques, on peut vouloir désactiver la fonction pour pouvoir se connecter à la base de données avec [Dbeaver](https://dbeaver.io) par exemple.

Pour autoriser l'accès, il faut donc ajouter `listen_addresses = '*'` dans le fichier de configuration de PostgreSQL `/etc/postgresql/13/main/postgresql.conf` dans la section `CONNECTIONS AND AUTHENTICATION`

Puis on édite le fichier `/etc/postgresql/13/main/pg_hba.conf` et on rajoute la ligne suivante à la fin du fichier :

```BASH
host  all monuser 0.0.0.0/0 md5
```

On redémarre PostgreSQL et on peut se connecter à la base avec un logiciel comme DBeaver.

### Installer votre application Symfony

#### Créer une clef SSH

Nous avons plusieurs moyens de nous connecter à GIT. Le plus simple à l'utilisation est d'utiliser une clef SSH.

Le principe, vous allez créer deux fichiers : l'un est votre clé privée, qui vous permet de vous identifier.
L'autre est une clé publique, qui permet de vérifier que votre clé privée est valide.

Le principe est qu'on peut vérifier la clé privée à partir de la clé publique, mais qu'à partir de la clé publique on ne peut pas générer une clé privée.

C'est donc un fichier (la clef privée) qui vous sert de login et mot de passe. On comprend donc aussi que ce n'est pas un fichier à donner à tout le monde.

Nous allons donc utiliser une clef privée pour se connecter à Gitlab. Pour que ça marche, on devra donner notre clé publique à Gitlab (pour qu'il puisse vérifier notre clef privée).

Sur votre ordinateur :

```bash
ssh-keygen -t ed25519 -C "nom de votre projet"
```

À la question `Enter file in which to save the key` vous mettez un nom lié à votre projet.
Exemple dans mon cas : `/Users/guillaumeferrari/.ssh/monprojet`
Ne mettez pas de passphrase (laissez vide deux fois).

Vous allez avoir deux nouveaux fichiers :

- la clef privée ici : `/Users/guillaumeferrari/.ssh/monprojet`
- la clef publique ici : `/Users/guillaumeferrari/.ssh/monprojet.pub`

##### Mettre la clé sur gitlab

- Récupérez le contenu de votre clef publique : `cat /Users/guillaumeferrari/.ssh/monprojet.pub` et copier la clef
- Allez sur la page : <https://gitlab.com/-/profile/keys> et coller dans le champs `key`

#### Mettre la clé sur votre serveur

- Récupérez le contenu de votre clef privée : `cat /Users/guillaumeferrari/.ssh/monprojet.pub` et copier la clef
- Créez un fichier sur votre serveur : `nano ~/.ssh/monprojet` et collez le contenu de votre clef privée.
- Créer un fichier : `nano ~/.ssh/config` dans lequel on va dire que pour se connecter a gitlab.com on doit utiliser la clef privée `monprojet`

```bash
Host gitlab.com
  HostName gitlab.com
  IdentityFile ~/.ssh/monprojet
  User git
```

Il faudra aussi que le dossier /var/www appartienne à l'utilisateur `debian` pour que celà fonctionne puisque votre clé ssh et le fichier config sont liés à cet utilisateur et non pas à `root` :
`sudo chown -R debian:debian /var/www/`

#### Git clone

Maintenant que tout est prêt, on va pouvoir installer notre application Symfony. Nous allons utiliser GIT pour récupérer les sources. Bien entendu, remplacez dans les commandes suivantes l'adresse du dépot par le votre.

```BASH
# On se place dans le dossier /var/www
# Vous pouvez choisir d'installer votre site à cet endroit, ou bien créer un sous-répertoire
cd /var/www
git clone git@gitlab.com:lozit/simplecrud.git
cd simplecrud 
```

On crée un fichier .env.local `nano .env.local` dans lequel on mets la connection avec la base de données et dans lequel on dit qu'on est sur une version de production :

```BASH
APP_ENV=prod
DATABASE_URL="postgresql://monuser:monpassword@localhost:5432/mabase"
```

Et ensuite on installe l'application norlmalement

```BASH
composer install
symfony console d:m:m
```

#### On configure Nginx

Enfin, on fait pointer notre serveur vers le nouveau répertoire de notre application : `/var/www/simplecrud/public`

en ditant le fichier de Nginx : `nano /etc/nginx/sites-enabled/monprojettest` et en changeant `root /var/www/html/monprojettest/public;` par `root /var/www/simplecrud/public;`

Et après un `systemctl restart nginx` on doit voir notre site en allant sur l'adresse ip du serveur.
Dans mon cas : [http://164.92.137.13/](http://164.92.137.13/)

<hr>

Première partie : [La création de l'application](https://github.com/lozit/Exercices-Webdev/blob/main/modules/Symfony/Simplecrud.md)
