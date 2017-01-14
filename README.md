# Préambule

Ce tutoriel a pour objectif d'apprendre à mettre en ligne une application web, dans le cadre du cycle 1 des ateliers Women On Rails.

# Sommaire

* [C'est quoi Heroku ?](#cestquoiHeroku)
* [Comment l'utiliser ?](#commentutiliser)
	* [Installer Heroku](#installerheroku)
		* [Avec Cloud9](#aveccloud9)
		* [Avec une installation native](#avecinstallationnative)
	* [Créer un compte Heroku](#compteheroku)
	* [Connecter son projet à son compte Heroku](#connecterprojetheroku)
	* [Changer le type de base de données utilisé](#changerbdd)
	* [Installer PostGreSql](#installerpostgres)
	* [Mettre à jour la configuration de la base de données](#majconfigbdd)
	* [Envoyer les modifications sur Github](#envoigithub)
	* [Envoyer les modifications sur Heroku](#envoiheroku)
	* [Visualiser son application en production](#visualisation)
* [Liens Utiles](#liensutiles)

# C'est quoi Heroku ? <a name="cestquoiHeroku"></a>

Heroku est un outil, administrable à partir d'un navigateur, qui permet d'héberger une application web dans le but de la rendre accessible à tous.

Voici quelques liens pour mieux comprendre ce service:
- [Top10 FAQ Heroku - plateforme pour innovations clients](https://www.linkedin.com/pulse/20141009115620-34850697-top10-faq-heroku-plateforme-pour-innovations-clients)
- [Wikipedia](https://fr.wikipedia.org/wiki/Heroku)

Nous allons utiliser ce service pour mettre en production notre application ``` Curiosities ```.

Mettre en prodution une application, c'est déployer la version 'finale' de l'application (ou d'une fonctionnalité de l'application) pour la rendre accessible aux futurs utilisateurs, après avoir effectué les développements et tous les tests nécessaires (unitaires, fonctionnels, charge et autres) à son bon fonctionnement.
C'est en général une version stable de l'application, sur laquelle aucun développement n'est effectué directement.

# Comment l'utiliser ? <a name="commentutiliser"></a>

## Installer Heroku <a name="installerheroku"></a>

### Avec Cloud9 <a name="aveccloud9"></a>

#### Mise à jour du service Heroku <a name="majheroku"></a>

Ouvrez votre projet. Heroku est installé de base sur Cloud9. Il faut en premier lieu vérifier qu'il est bien à jour.
Pour cela, tapez dans le terminal la commande suivante qui fera les mises à jour automatiquement si besoin :

``` Terminal
wget -O- https://toolbelt.heroku.com/install-ubuntu.sh | sh
````
> Astuce : Pour taper le symbole ``` | ```, faites Alt + Shift + L sur Mac ; ou retrouvez les raccourcis pour les autres claviers [ici](http://atlasti.com/faq/where-is-the-vertical-bar-on-my-keyboard/)

### Avec une installation native <a name="avecinstallationnative"></a>

Installez la version de Heroku qui vous correspond en vous aidant de ce [lien](https://devcenter.heroku.com/articles/heroku-command-line#download-and-install).

## Créer un compte Heroku <a name="compteheroku"></a>

Allez sur le site d'[Heroku](https://signup.heroku.com/identity) et créez-vous un compte.

![Compte Heroku](/images/readme/heroku_new_account.png)

> Astuce : Pour connaître la version de Heroku installée sur votre machine (si vous rencontrez des soucis), vous pouvez taper la commande suivante dans votre terminal : ``` heroku --version ```

![Version Heroku](/images/readme/heroku_version.png)

## Connecter son projet à son compte Heroku <a name="connecterprojetheroku"></a>

Retournez sur votre projet Cloud9 et tapez la commande suivante dans le terminal : ``` heroku login ```
Cette commande permet de vous authentifier par rapport à Heroku.

Il vous sera demandé l'adresse email et le mot de passe que vous avez utilisés pour créer votre compte Heroku.

![Login Heroku](/images/readme/heroku_login.png)

Puis, pour initialiser votre projet du coté de Heroku, tapez la commande suivante dans votre terminal : ``` heroku create ```
Si vous retournez sur votre compte [heroku, onglet 'dashboard'](https://dashboard.heroku.com/apps), une nouvelle application devrait être apparue.

Heroku génère un nom aléatoire pour chaque application qu'il crée. Ici par exemple, l'application créée se nomme ```peaceful-refuge-57279```.

![Dashboard](/images/readme/heroku_dashboard.png)

N'hésitez pas à cliquer sur cette nouvelle application et à naviguer à travers les catégories (ressources, déploiement, activité, etc).

![Dashboard / Activity](/images/readme/heroku_activity_dashboard.png)

Vous pouvez notamment proposer un nouveau nom pour votre application dans ``` Settings > Name ```. Attention, il faudra choisir un nom qui n’est pas déjà pris !

![Dashboard / Settings](/images/readme/heroku_settings_name_change.png)

## Changer le type de base de données utilisé <a name="changerbdd"></a>

### Installer PostGreSql <a name="installerpostgres"></a>

Heroku ne supporte malheureusement pas le type de base de données ```sqlite``` car ce n'est pas un type de base de données adapté pour une utilisation intensive et stable (nécessaire dans notre application déployée en 'production'). Nous allons donc le changer pour utiliser ```postgreSQL```.

Vous pouvez trouver plus d'informations [ici](https://devcenter.heroku.com/articles/sqlite3) concernant ce changement de base de données.

Dans votre application, ouvrez le fichier ```gemfile```.

![Gemfile original](/images/readme/gemfile_before.png)

On veut installer l'adaptateur ```PostGreSQL``` et l'utiliser en mode ```production``` à la place de l'adaptateur ```SQLite```. 

Pour cela, spécifiez que la ligne ``` gem 'sqlite3' ``` doit être utilisée seulement pour les modes ```development``` et ```test```.
Puis, ajoutez la ligne ``` gem 'pg' ``` en spécifiant qu'elle doit être utilisée seulement pour le mode ```production```.

![Gemfile modifié](/images/readme/gemfile_after.png)

Ensuite, lancez la commande suivante dans votre terminal pour mettre à jour vos gems : ``` bundle install ```.

### Mettre à jour la configuration de la base de données <a name="majconfigbdd"></a>

L'installation terminée, ouvrez le fichier ``` config/database.yml ```. Il devrait actuellement ressembler à cela :

![Database Configuration](/images/readme/database_before.png)

Dans la section ```production```, changez la ligne ``` adapter: sqlite3 ``` par ``` adapter: postgresql ```.
Puis changez le nom de votre base de données de production. Par exemple, ``` db/production.sqlite3 ``` devient ``` curiosities_production ```.

Ici, on choisit d'appeler notre base de données ``` curiosities_production ``` car elle contient toutes les données de production pour le projet ``` Curiosities ```.

![Database Configuration](/images/readme/database_after.png)

N'oubliez pas ensuite de sauvegardez votre fichier.

### Envoyer les modifications sur Github <a name="envoigithub"></a>

N'oubliez pas d'enregistrer vos modifications, de les committer et de les envoyer sur votre répertoire distant (GitHub). Vous pouvez vous aider de ce [tutoriel](https://women-on-rails.github.io/guide/push_project).

### Envoyer les modifications sur Heroku <a name="envoiheroku"></a>

Pour faire en sorte qu'Heroku prenne en compte vos nouvelles modifications, committez les (si ce n'est pas déjà fait à l'étape précédente) puis tapez la commande suivante dans votre terminal: ``` git push heroku master ```.

Tout comme pour GitHub, cela envoie vos nouveaux commits à votre répertoire distant sur Heroku.

Lorsque vous avez lancé cette commande, une URL s'est affichée dans votre terminal. C'est l'URL de production de votre application ! 

![Url production](/images/readme/git_push_heroku_master.png)

Si vous consultez cette URL maitenant, vous n'y trouverez pas grand-chose car l'application ne sait pas encore où trouver les données à afficher.

### Mettre à jour la base de données sur Heroku <a name="cestquoiHeroku"></a>

Nous avons maintenant besoin de mettre à jour notre base de données ```PostGreSql``` pour ajouter les tables qui la composent (dans notre cas, il y a une table nommée ```curiosities```).

Pour cela, tapez la commande ``` heroku run rake db:migrate ``` dans votre terminal.

### Visualiser son application en production <a name="visualisation"></a>

Vous pouvez maintenant cliquer sur l'URL de production de votre application et vérifier que votre application tourne.

![Visualisation](/images/readme/heroku_view_app.png)

Puis, utilisez-la pour créer de nouvelles curiosités dans votre base de données!

![Visualisation avec curiosité](/images/readme/heroku_view_app_curiosity.png)

# Liens Utiles : <a name="liensutiles"></a>
- La documentation de Ruby : http://ruby-doc.org/core-2.3.1/
- La documentation de Ruby On Rails : http://api.rubyonrails.org/
- Heroku : https://www.heroku.com/
- La documentation pour déployer via Cloud9 : https://docs.c9.io/docs/deploying-via-cli
- La documentation d'Heroku pour Ruby On Rails 4 : https://devcenter.heroku.com/articles/getting-started-with-rails4

# Et ensuite ?
N'hésitez pas à faire un tour sur le [guide des Women On Rails](https://women-on-rails.github.io/guide/) pour des rappels de commandes, de l'aide ou de nouveaux tutoriels.

Pour en découvrir plus sur le monde du Web et la communauté Ruby On Rails, n'hésitez pas à venir aux [ateliers libres des Women On Rails](https://www.meetup.com/fr-FR/Women-On-Rails/), aux meetups de (Rails thematic Workshops)[https://www.meetup.com/fr-FR/rails-thematic-workshops/], aux meetups de (Paris Ruby Workshop)[https://www.meetup.com/fr-FR/Paris-Ruby-Workshop/] ou encore à découvrir les rendez-vous mensuels de (ParisRB)[https://www.meetup.com/fr-FR/parisrb/] !
