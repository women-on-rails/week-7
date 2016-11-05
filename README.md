# Préambule

Slides du cours disponibles [ici](http://slides.com/women_on_rails/week-5)

Ce tutoriel a pour objectif de comprendre comment manipuler des objets dans le controleur et passer les données de ces objets à des vues (creation et édition), dans le cadre du cycle 1 des ateliers Women On Rails.

# Étape 1 : Rappels

## Commandes principales

Vous pouvez retrouver les commandes utiles pour le terminal, git et la console Ruby On Rails [ici](https://women-on-rails.github.io/guide/main_commands).

## MVC

Le rappel sur le patron de conception [Modèle - Vue - Controleur] peut etre trouvé [ici](openclassrooms.com/courses/apprendre-asp-net-mvc/le-pattern-mvc)

## Actions HTTP

Ruby On Rails permet d'utiliser au mieux le [protocole HTTP](https://openclassrooms.com/courses/les-requetes-http), sur lequel repose la navigation Web. Il y a 4 types de requêtes principales en HTTP :
- GET (afficher une page),
- POST (créer une nouvelle ressource),
- PUT (pour modifier entièrement la ressource, ou PATCH pour la modifier partiellement),
- DELETE (supprimer une ressource).

Suite à chaque requête, le serveur envoie une réponse.

De plus, il y a 7 actions de base dans chaque contrôleur Rails:
- SHOW : affiche une ressource en particulier (action GET)
- INDEX : affiche la liste de toutess les ressources d'un meme type (action GET)
- NEW : affiche le formulaire pour créer une nouvelle ressource (action GET)
- CREATE : une fois le précédent formulaire complété, crée la ressource (action POST)
- EDIT : affiche le formulaire d’édition d'une ressource (action GET)
- UPDATE : met a jour une ressource spécifiée (action PUT)
- DESTROY : supprime une ressource spécifique (action DELETE)

# Étape 2 : Lire l'exercice et se lancer

## Application au projet Curiosités

Ouvrez votre projet avec Cloud9, ou l'éditeur que vous utilisez si vous avez une installation native.

Si vous utilisez SublimeText, vous pouvez faire ```subl .``` dans la console pour ouvrir directement votre projet. (subl c'est SublimeText, l'espace c'est parce que la commande est finie, et le point c'est pour dire "ouvre dans Sublime Text tout le dossier dans lequel je suis, en un coup").

## Ajout d'un formulaire de création des curiosités

Nous avons créé des moyens d'afficher ou détruire une curiosité spécifique lors du tutoriel [week-5](https://github.com/women-on-rails/week-5). Nous allons maintenant faire en sorte de pouvoir créer une nouvelle curiosité.

Si l'on reprend les actions de base d'un controleur Rails, voici celles qui nous intéressent:
- NEW : affiche le formulaire pour créer une nouvelle ressource (action GET)
- CREATE : une fois le précédent formulaire complété, crée la ressource (action POST)

Allons ajouter ces deux nouvelles méthodes à notre controleur ``` CuriositiesController ```.

### Ajout de la méthode ```new``` dans le controleur ```CuriositiesController```

Ouvrez le fichier ``` app/controllers/curiosities_controller.rb```. Pour le moment, il contient les méthodes ``` show ``` et ``` destroy ```. Ajoutons-y la méthode ``` new ``` qui permet d'instancier une nouvelle curiosité que nous utiliserons dans le formulaire de création.

! IMAGE !

Maintenant, occupons-nous de la vue associée à cette méthode ```new```.

### Ajout du formulaire de création

Créez le fichier ``` app/views/curiosities/new.html.erb ```.
Ce fichier va contenir un formulaire permettant de créer une curiosité composée d'un nom (``` name ```), d'une description (``` description ```), d'un lien vers une image (``` image_url ```) et d'une description relative à l'image (``` image_text ```).

Voici comment serait un formulaire créé simplement avec du HTML et du CSS version Bootstrap :
``` HTML
<h1> Création d'une nouvelle curiosité </h1>

<form action="/curiosities" method="post">
  <div class="form-group">
    <label for="curiosityName">Name</label>
    <input type="text" class="form-control" name="curiosity[name]">
  </div>
  <div class="form-group">
    <label for="curiosityDescription">Description</label>
    <input type="text" class="form-control" name="curiosity[description]">
  </div>
  <div class="form-group">
    <label for="curiosityImageUrl">Image Url</label>
    <input type="text" class="form-control" name="curiosity[image_url]">
  </div>
  <div class="form-group">
    <label for="curiosityImageText">Image text</label>
    <input type="text" class="form-control" name="curiosity[image_text]">
  </div>
  <input type="submit" value="Créer la curiosité" class="btn btn-default" />
</form>
```

Nous utilisons Ruby On Rails pour créer notre application. Nous allons donc utiliser plus que du HTML pour générer notre formulaire. Cela va nous éviter des erreurs, ajouter de la logique de sécurité automatiquement et permettre de réutiliser ce formulaire par la suite pour l'édition des curiosités.

Voici le code de notre formulaire, avec du HTML et des outils fournis par Ruby On Rails :

``` Ruby
<h1> Création d'une nouvelle curiosité </h1>

<%= form_for :curiosity, url: curiosities_path do |c| %>
  <div class="form-group">
	<%= c.label :name %>
    <%= c.text_field :name, class: 'form-control' %>
  </div>
  <div class="form-group">
	<%= c.label :description %>
    <%= c.text_field :description, class: 'form-control' %>
  </div>
  <div class="form-group">
	<%= c.label :image_url %>
    <%= c.text_field :image_url, class: 'form-control' %>
  </div>
  <div class="form-group">
	<%= c.label :image_text %>
    <%= c.text_field :image_text, class: 'form-control' %>
  </div>

  <%= c.submit 'Créer la curiosité', class: "btn btn-default" %>
<% end %>
````
#### Explication du formulaire

Avec Ruby On Rails, vous pouvez créer des formulaire de la manière suivante:
``` Ruby
<%= form_for :curiosity, url: curiosities_path do |c| %>
````

Cette ligne permet d'ouvrir un formulaire.
Le formulaire se finit toujours par <% end %> car c'est un bloc.

Ici, ``` curiosities_path ``` réfère à l'action à effectuer avec les données du formulaire quand l'utilisateur clique sur le bouton `Créer la curiosité`. Notez que le formulaire fait des requetes HTTP ``` POST ``` ce qui permet au controleur de savoir quelle méthode utiliser.

La section suivante permet d'ajouter un champ texte à votre formulaire, avec son label:
``` Ruby
  <div class="form-group">
	<%= c.label :name %>
    <%= c.text_field :name, class: 'form-control' %>
  </div>
````

La section suivante permet de créer le bouton pour soumettre le formulaire:
``` Ruby
  <%= c.submit 'Créer la curiosité', class: "btn btn-default" %>
````

### Ajout des nouvelles routes

Nous avons créé la méthode ``` new ``` dans le controleur ``` CuriositiesController``` et la vue associée. Maintenant, nous devons permettre à un utilisateur d'aller sur cette vue. Pour cela, ouvrez le fichier ``` config/routes.rb ``` et ajoutez-y deux nouvelles routes:

! IMAGE !

La ligne ``` get '/curiosities/new', to: 'curiosities#new' ``` permet de rediriger l'utilisateur vers la page de création d'une curiosité.

La ligne ``` post '/curiosities', to: 'curiosities#create', as: 'curiosities' ``` dit au controleur qu'avec les données du formulaire, on veut utiliser ce qui est contenu dans une méthode ``` create```.

> Astuce : Notez que le code ``` , as: 'curiosities' ``` a été enlevé de la route ``` delete '/curiosities/:id', to: 'curiosities#destroy' ``` et ajouté à la route ``` post '/curiosities', to: 'curiosities#create' ```.
> Ce bout de code ne doit etre écrit qu'une fois. De plus la position des routes est importante.
> Si vous obtenez une erreur, vérifiez l'ordre de vos routes.

### Ajout de la méthode ``` create ``` dans le controleur ``` CuriositiesController ```

A ce stade, nous avons un formulaire mais ne savons pas comment utiliser les donnés qu'il contient.

Ouvrez de nouveau le fichier ``` app/controllers/curiosities_controller.rb```. Ajoutons-y la méthode ``` create ``` qui permet de sauvegarder en base de données une nouvelle curiosité dont nous avons récupéré les informations grace au formulaire de création.

! IMAGE !

Et maintenant, testez votre formulaire !

# Étape 3 : Pour aller plus loin

## Ajout du formulaire de création

! EN CONSTRUCTION !

## Ajout d'une pop-up de confirmation

Nous aimerions ajouter une pop-up de confirmation avec ````data: { confirm: 'Message de confirmation' }```` dans le ````link_to```` de la vue, car c'est une action sur laquelle l'utilisateur ne pourra pas revenir. Nous voulons donc être sûrs de son choix.

Aidez-vous de la documentation du [link_to](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to) et ajoutez cette pop-up !

## Ajout d'une image

Pour rendre notre page plus sympa, nous aimerions avoir une image à la place du texte Supprimer. Amusez-vous à remplacer ce texte par une icône en utilisant les icônes bootstrap, dont vous pouvez trouver la documentation [ici](http://getbootstrap.com/components/) !

# Étape 4 : Enregistrer les modifications sur le répertoire distant

[Enregistrer vos modifications et les envoyer sur votre répertoire Github](https://women-on-rails.github.io/guide/push_project)

# Liens Utiles :
- La documentation de Ruby : http://ruby-doc.org/core-2.3.1/
- La documentation de Ruby On Rails : http://api.rubyonrails.org/
- Active Record : https://fr.wikipedia.org/wiki/Active_record (concept en français) ou http://guides.rubyonrails.org/active_record_basics.html (introduction de Rails, en anglais)
- Les routes dans Ruby On Rails : http://guides.rubyonrails.org/routing.html