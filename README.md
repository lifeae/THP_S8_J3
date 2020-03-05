# The To do List Project

[![forthebadge](https://forthebadge.com/images/badges/made-with-ruby.svg)![forthebadge](http://forthebadge.com/images/badges/built-with-love.svg)](http://forthebadge.com)

## Comment faire fonctionner ce bin's :
* Ouvrir le terminal et se déplacer jusqu'à l'emplacement de ce dossier
* Vérifie dans le gemfile que tu possède déjà la version de ruby et de toutes ses gems dont on va avoir besoin. Si certaines te manquent, tu les installes.
* `bundle install`,
* `rails db:migrate`
* `rails db:seed`
* `rails server`
* Se promener dans l'arborescence : http://localhost:3000/


## Projet : To do List with Ajax

### 1. Introduction

Aujourd’hui, nous allons travailler à nouveau sur notre idée à 1 milliard de dollars : la fameuse appli de To do listes. Je te laisse le choix entre repartir de ton app d'hier ou créer un nouveau projet à partir du repo initial.
### 2. Le projet
#### 2.1. Créer des tâches en AJAX

Ici, nous allons mettre en œuvre un usage très fréquent d'AJAX : la création d'entrées en BDD. On va donc bosser sur la page d'accueil et se débrouiller pour que le formulaire de création de tâche rajoute, sans rechargement de la page, les nouvelles tâches créées dans la liste adéquate.

Sauf si tu l'as cassé, le formulaire de création de tâche de la page d'accueil doit bien fonctionner (teste-le si besoin). Il ne te reste alors qu'à appliquer la recette que je t'ai donné dans le cours :

    Permettre au formulaire de faire une requête en AJAX ;
    Mettre à jour la bonne méthode de controller pour qu'elle sache gérer une requête AJAX ;
    Créer et coder le fichier JavaScript qui va permettre de rajouter la nouvelle tâche sous la bonne catégorie.

Comme d'habitude, pense à bien procéder par étape (si tu fais tout en même temps et que ça bugue, comment savoir quelle étape est en faute ?) et n'hésite pas à rajouter, dans le fichier html de la homepage, des id et class aux bons endroits pour simplifier le travail de modification du DOM.

Pense à l'esthétique de ton rendu final : n'hésite pas à coder une animation qui fait que la tâche s'ajoute de façon stylée (JQuery peut te faciliter la vie de ce point de vue là).
#### 2.2. Supprimer les tâches en AJAX

Maintenant que tu as réussi à faire fonctionner cette première fonctionnalité en AJAX, il serait logique d'implémenter son inverse : la possibilité de supprimer une tâche. Indispensable pour éviter de rester bloqué avec une tâche insurmontable, genre sortir les poubelles.

Ajoute à chaque tâche une petite icône que tu peux trouver sur font-awesome (oui parce qu'on est gentils, on t'a mis font-awesome sur l'app) qui te servira de bouton "supprimer". Justement, une petite poubelle ça fera bien l'affaire.

Évidemment, tu commenceras par faire fonctionner le bouton sans AJAX avant de le modifier pour qu'il soit en remote: true... Tu n'es pas de ceux qui ne savent pas procéder pas à pas, hein ?

Tout comme précédemment : pense à faire une petite animation sympa au moment de supprimer une tâche. Mais n'y passe pas non plus des heures : c'est pour faire joli !
#### 2.3. Changer le statut des tâches en AJAX

Bien, allons un peu plus loin dans l'AJAX maintenant que tu as implémenté 2 exemples très courants. L'app Rails que je t'ai passé prévoit la possibilité de suivre le statut d'une tâche : false si elle est toujours à faire, true si elle est terminée. On va piloter par AJAX cette information, à nouveau directement depuis la page d'accueil. Divisons ça en plusieurs petites sous parties :
a) Changement de statut en BDD via une checkbox

On va ajouter une checkbox à côté de chaque tâche. L'objectif est que lorsqu'elle sera cliquée, un appel en AJAX aura lieu afin de changer le statut en BDD de la tâche concernée.

Attention je te préviens, là on sort du cadre strict du cours de ce matin. En effet, tu vois bien que je te demande de faire une requête AJAX là où il n'y a pas, initialement, de lien du tout. Le clic sur une checkbox ne déclenche pas de requête HTTP... Alors comment s'en sortir ?

Commence par jeter un œil à la partie "3.2 Customize remote elements" de la documentation de Rails. On y explique comment rajouter une checkbox qui déclenche des requêtes AJAX.
Ensuite, on va utiliser le helper Rails check_box_tag pour suivre ce que nous dit la documentation. Voici, pour t'aider, la syntaxe qu'il faut suivre :

 <%= check_box_tag :name, 1, CheckedOuPas? , data: { remote: true, url: TonURL, params: { id: ExampleID, status: StatusValue}.to_param, method: "put" }%>

Je t'explique rapidement la syntaxe ci-dessus afin que tu la comprennes bien :

    :name est le nom de la checkbox (il faut en donner un...)
    1 c'est la valeur qui sera renvoyée sous params[:name] si on coche la box. Si elle n'est pas cochée, ça ne renvoie rien
    A la place de CheckedOuPas? tu dois mettre false ou true selon si tu veux que ta box soit initialement cochée ou non.
    Ensuite on envoie plein de données sous data, à commencer par data-remote qu'on met bien sûr à true car c'est de l'AJAX.
    Après, remplace TonURL par l'URL qui doit recevoir la requête AJAX. Elle apparaîtra sous le champ data-url une fois le helper transformé en checkbox HTML.
    Après tu peux passer plein de données sous params. Dans l'exemple je t'ai mis les exemples pour mettre des infos sous params[:id] et params[:status] : charge à toi de mettre les bonnes valeurs.
    La fonction .to_param est indispensable pour que les données que tu envoies dans le array params soient bien au bon format.
    Et on finit par un classique data-method qu'on met à PUT car tu vas probablement passer par la méthode update du controller task dont la route est en PUT, non ? (indice : oui).

Parfait. Je ne t'en dis pas plus : charge à toi de tester cette syntaxe et de passer par la méthode update du controller task pour mettre à jour le status des tâches. Mets des puts pour voir dans ton terminal ce qui se passe dans ta méthode, inspecte le html de la page pour voir la tronche de ta checkbox et regarde la console JS du navigateur pour identifier un éventuel bug. Bref : cherche un peu seul !

Comment savoir si ta checkbox marche ? Si tu rafraîchis ta page, elle doit être cochée si la tâche est en status true, non-cochée dans le cas contraire. En parallèle, tu peux vérifier dans la console Rails que la BDD est bien modifiée suite à clic sur la checkbox : le statut de la tâche doit bien être mis à jour.
#### 2.2 Réaction en front via la checkbox

C'est important que l'utilisateur ait un retour visuel de ses actions. Ajoute un moyen de voir explicitement si la tâche est bien terminée (status = true) : par exemple tu peux barrer le texte en JS ou déplacer la tâche dans une autre liste. Etc.

Ce qui est primordial c'est que tout changement apporté par le JS de l'action AJAX soit le même que si l'utilisateur rafraîchissait sa page. Ainsi, si (via la requête AJAX) tu barres le texte d'une tâche terminée, il faut que (de base), toutes les tâches terminées soient affichées barrées au chargement initial de la page. Comme ça quoique fasse l'utilisateur, qu'il quitte la page, y revienne, fasse des appels AJAX, etc. il verra toujours les tâches affichées de la même façon : barrées si elles sont finies, normales si elles sont à finir.
#### 2.3 Une animation pour envoyer du lourd

Pimp un peu ça en faisant en sorte que le texte se barre progressivement par exemple, ou que le texte disparaisse de manière sympa. Fais preuve d'imagination !

Tu peux aller jeter un oeil sur des sites comme codeMyUi pour trouver un peu d'inspiration.

Là on est clairement dans le bonus : je te laisse chercher ce qui te semble beau et faisable !
### 3. Rendu attendu

Un repo GitHub avec une app Rails complète.
