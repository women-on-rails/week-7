# Préambule

Ce tutoriel a pour objectif d'apprendre à mettre en ligne une application Web, dans le cadre du cycle 1 des ateliers Women On Rails.

# C'est quoi Heroku ?

Heroku est un outil, administrable à partir d'un navigateur, qui permet d'héberger une application Web dans le but de la rendre accessible à tous.

Voici quelques liens pour mieux comprendre ce service:
- [Top10 FAQ Heroku - plateforme pour innovations clients](https://www.linkedin.com/pulse/20141009115620-34850697-top10-faq-heroku-plateforme-pour-innovations-clients)
- [Wikipedia](https://fr.wikipedia.org/wiki/Heroku)

# Comment l'utiliser ?

## Installation de Heroku

> Astuce : Pour connaitre la version de Heroku installée sur votre machine, vous pouvez taper la commande suivante dans votre terminal : ``` heroku --version ```

### Avec Cloud9

#### Vérification de la version installée

Ouvrez votre projet. Heroku est installé de base sur Cloud9. Il faut en premier lieu vérifier qu'il est bien à jour.
Pour cela, tapez dans le termnial la commande suivante :

``` Terminal
wget -O- https://toolbelt.heroku.com/install-ubuntu.sh | sh
````

![Version Heroku](/images/readme/heroku_version.png)

### Avec une installation native

Installez la version de Heroku qui vous correspond en vous aidant de ce [lien](https://devcenter.heroku.com/articles/heroku-command-line#download-and-install).

## Création d'un compte Heroku

Allez sur le site d'[Héroku](https://signup.heroku.com/identity) et créez-vous un compte.

![Compte Heroku](/images/readme/heroku_new_account.png)

## Connecter son projet à son compte Heroku

Retournez sur votre projet Cloud9 et tapez la commande suivante dans le terminal : ``` heroku login ```
Cette commande permet de s'authentifier par rapport à Heroku.

Il vous sera demandé l'adresse email et le mot de passe que vous avez utilisé pour créer votre compte Heroku.

![Login Heroku](/images/readme/heroku_login.png)

Puis, pour initialiser votre projet du coté de Heroku, tapez la commande suivante dans votre terminal : ``` heroku create ```
Si vous retournez sur votre compte [heroku, onglet 'dashboard'](https://dashboard.heroku.com/apps), une nouvelle application devrait etre apparue.

Heroku génère un nom aléatoire pour chaque application qu'il crée. Ici par exemple, l'application créée se nomme ```peaceful-refuge-57279```.

![Dashboard](/images/readme/heroku_dashboard.png)

N'hésitez pas à cliquer sur cette nouvelle application et à naviguer à travers les catégories (ressources, déploiement, activité, etc).

![Dashboard / Activity](/images/readme/heroku_activity_dashboard.png)

## Changer le type de base de données utilisé

### Installation de PostGreSql

Herou ne supporte malheureusement pas le type de base de donnée ```sqlite``` car ce n'est pas un type de base de données prévu pour une utilisation intensive et stable ('production'). Nous allons donc le changer pour utiliser ```postgreSQL```.

Vous pouvez trouver plus d'informations [ici](https://devcenter.heroku.com/articles/sqlite3) concernant ce changement de base de données.

Dans votre application, ouvrez le fichier ```gemfile```.

![Gemfile original](/images/readme/gemfile_before.png)

Spécifiez que la ligne ``` gem 'sqlite3' ``` doit etre utilisée seulement pour les modes ```development``` et ```test```.
Puis, ajoutez la ligne ``` gem 'pg' ``` en spécifiant qu'elle doit etre utilisée seulement pour le mode ```production```.

![Gemfile modifié](/images/readme/gemfile_after.png)

Cela veut dire qu'on veut installer l'adaptateur ```PostGreSQL``` et qu'on l'utilise en mode ```production``` à la place de l'adaptateur ```SqLite```.

Ensuite, lancez la commande suivante dans votre terminal pour mettre à jour vos gem : ``` bundle install ```.

### Mettre à jour la configuration de la base de données

L'installation terminée, ouvrez le fichier ``` config/database.yml ```. Il devrait actuellement ressembler à cela :

![Database Configuration](/images/readme/database_before.png)

Dans la section ```production```, changez la ligne ``` adapter: sqlite3 ``` par ``` adapter: postgresql ```.
Puis changez le nom de votre base de données de production. Par exemple, ``` db/production.sqlite3 ``` devient ``` production ```.

N'oubliez pas de sauvegardez votre fichier.

![Database Configuration](/images/readme/database_after.png)

### Envoi des modifications sur Github

N'oubliez pas d'enregistrer vos modifications et de les envoyer sur votre répertoire distant (Github). Vous pouvez vous aider de ce [tutoriel](https://women-on-rails.github.io/guide/push_project).

### Envoi des modifications sur Heroku

Pour faire en sorte qu'Heroku prenne en compte vous nouvelles modifications, committez les puis tapez la commande suivante dans votre terminal: ``` git push heroku master ```.

### Mise à jour de la base de données sur Heroku

Nous avons maintenant besoin de mettre à jour notre base de données ```PostGreSql``` pour ajouter les tables qui la composent. Pour cela, tapez la commande ``` heroku run rake db:migrate ``` dans votre terminal.

### Visualisation de l'application en production

Lorsque vous avez lancé la commande ``` git push heroku master  ```, une url s'est affichée dans votre terminal. C'est l'url de production de votre application.

![Url production](/images/readme/git_push_heroku_master.png)

Cliquez dessus et vérifiez que votre application tourne !

![Visualisation](/images/readme/heroku_view_app.png)

# Liens Utiles :
- La documentation de Ruby : http://ruby-doc.org/core-2.3.1/
- La documentation de Ruby On Rails : http://api.rubyonrails.org/
- Heroku : https://www.heroku.com/
- La documentation pour déployer via Cloud9 : https://docs.c9.io/docs/deploying-via-cli
- La documentation d'Heroku pour Ruby On Rails 4 : https://devcenter.heroku.com/articles/getting-started-with-rails4