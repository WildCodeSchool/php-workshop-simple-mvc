# Le Simple MVC

## Simple comme noyer un bœuf avant d'avoir vendu le poisson

#### 💡 Introduction

Le Simple-MVC est un framework "maison", développé par tes formateurs pour faciliter l'apprentissage de l'architecture MVC "Modèle - Vue - Contrôleur". Il est relativement facile à prendre en main et est fourni avec des fichiers d'exemple qui illustrent son fonctionnement : ce sont tous les fichiers/dossiers dont le nom contient le mot "Item", dans le dossier *src/*. Tu as déjà étudié ces principes dans de précédents ateliers, mais il est temps de pratiquer avec un cas concret. Ici, en t'inpirant des exemples du Simple-MVC, tu vas créer une route afin de lister tous tes animaux de compagnie.

#### 🛠️ Avant de commencer : les prérequis

Clone [le repo Github du Simple-MVC](https://github.com/WildCodeSchool/simple-mvc) sur ta machine.

Dans ton serveur MySQL, créé une *database* qui contient une table *"pet"* (= "animal de compagnie") avec 3 champs :

* id
* name
* species (qui contiendra *cat* 🐱 or *dog* 🐶)

Ajoute quelques valeurs dans cette table.

> Note : n'oublie pas de configurer la connexion à ton serveur MySQL. Pour cela, il faut créer un fichier *db.php* à partir du fichier *db.php.dist* ❗ Le fichier *db.php* contenant des informations sensibles, il ne sera pas versionné. Le fichier *db.php.dist* sert de modèle et ne doit pas être effacé.

#### 1️⃣ Première étape : le contrôleur

Dans un premier temps, tu veux lister tes *pets* sur une page.

Sans te soucier des requêtes en base de données, ni même des vues, créé donc danns le dossier *src/Controller* une classe `PetController`, avec une seule méthode nommée `index()`, qui va retourner le nom de ton *pet* préféré, "bibi" par exemple 🙃).

Cette classe devra se trouver dans le namespace `App\Controller`. `App` car toutes les classes que tu vas créer toi même devront par convention s'y trouver (c'est le namespace racine de ton **App**lication), et `Controller` parce-que, et bien, c'est un contrôleur. 🙃

Maintenant que ta méthode existe, comment faire pour afficher le résultats ? Quelle adresse saisir dans la barre d'URL de ton navigateur ? Cette adresse (on parle plutôt de "route") va être envoyée à ton *front contrôleur*, le fichier *index.php* qui sera le point d'entrée de toutes tes pages. Regarde le, il n'y a pas grand chose dedans. Ce fichier, en plus d'initialiser quelques autres fichiers, fait appel à *routing.php*, le routeur.
Le but de ce routeur est d'appeler la bonne méthode du bon contrôleur, en fonction de la route. Que veux tu faire ici ? 
Tu veux appeler la méthode `index()` de `PetController`. Dans l'atelier Marmiwild, tu avais créé un routeur à l'aide de plusieurs conditions. On aurait pu faire la même chose dans le Simple-mvc, mais l'implémentation choisie est différente. Par "simplification" une route aura un format bien défini, qui sera : `/nomDuController/nomDeLaMethode` (pour le nom du contrôleur, ça sera le début de nom de la classe, sans le suffixe 'Controller').

Exemple : `/item/add` signifie que tu fais appel à la methode `add()` du `ItemController`.

Pour appeler la méthode `index()` de ton PetController, tu devras donc saisir `/pet/index`.

Lance ton serveur PHP, ouvre ton navigateur, et va à l'adresse `localhost:8000/pet/index`. Si *"bibi"* (ou la chaîne de caractères que tu as choisie) s'affiche, c'est bon pour la suite! 👍

> Note : tu peux prendre exemple sur le **ItemController**.

#### 2️⃣ Deuxième étape : la vue

C'est bien beau un contrôleur comme ça, mais on aimerait quand même qu'il nous renvoie une vue, et pas juste "bibi"... 🤔

Dans le dossier `View`, créé donc un dossier `Pet`, dans lequel tu vas ajouter un fichier *index.html.twig*.

Ce fichier est une vue Twig, qui va hériter de *layout.html.twig*. La première chose à faire est donc de rajouter un `extends` au tout début de ton fichier *index.html.twig*. Ajoute ensuite un `block content` dans lequel tu peux ajouter uniquement un simple `h1` contenant le titre de ton choix (comme *"My Pets"*). Nous verrons plus tard pour ajouter d'autres choses à cette vue. N'hésite pas à regarder le fichier d'exemple *Item/index.html.twig* pour t'aider.

Maintenant que ta vue est prête, il faut retourner dans ton contrôleur et dire à ta méthode **index** de compiler et retourner la vue Twig que tu as créée, à la place de *"bibi"*.

Pour compiler une vue twig avec le Simple MVC, la syntaxe est :

```php
$this->twig->render('Folder/file.html.twig')
```
Twig te renvoie alors une chaîne de caractères contenant du HTML. N'oublie pas de mettre un `return` devant si tu veux que ta méthode renvoie à son tour quelque chose !

⚠️ Attention, pour le moment ton code risque de ne pas fonctionner. En effet, tu utilises Twig, plus précisemment la méthode `render()` via le `$this->twig->render()`. Mais pour cela il faut déjà instancier un objet de type `Twig` ! Plutôt que de s'embêter à refaire cette instanciation dans toutes les méthodes de tous tes contrôleurs, ce qui ne serait pas très DRY, le Simple-MVC l'a déjà fait pour toi dans une classe dédiée, l'`AbstractController`. Tu n'as pas à y toucher, par contre, pour profiter des méthodes utiles qui y sont définies, fais juste en sorte que ton `PetController` hérite de cette classe en ajoutant un `extends`. C'est tout l'intérêt d'utiliser un *framework*, les tâches génériques qui n'apportent pas de réel intérêt métier à ton application sont déjà là, afin de te faire gagner du temps. 😉

> Réactualise, ta page devrait correctement s'afficher maintenant!

#### 3️⃣ Troisième étape : le modèle

Ok! On a bien un contrôleur qui renvoie une vue, on est pas mal! Mais maintenant, ça pourrait être cool d'aller chercher tous tes animaux de compagnie dans ta base de données, et d'en afficher les informations dans ta vue, non? Et bien faisons cela! 😎

Tu as une table *"pet"* dans ta base de données, tu vas donc avoir besoin d'un **PetManager**, qui va servir à faire des requêtes vers cette table. À toi de le créer dans le dossier `src/Model`!

En suivant la même logique que pour le contrôleur, à toi de deviner dans quel *namespace* cette classe sera rangée. 😉

Là encore, tu te rends compte qu'il existe déjà un **AbstractManager** qui possède quelques méthodes génériques. Sympa, le `SELECT *` dont on a besoin pour récupérer tous nos animaux de compagnie est déjà défini ici! Du coup, il suffit que ton **PetManager** hérite d'**AbstractManager**. Il ne te reste qu'une chose à configurer : la constante de classe TABLE qui doit contenir le nom de ta table en base de données, et le tour est joué! 👍 

> Note : là encore, n'hésite pas à regarder l'exemple de l'`ItemManager` pour mieux comprendre l'articulation entre tous ces fichiers.

#### 4️⃣ Quatrième étape : envoyer les données dans la vue

On approche de la fin! Il ne nous reste plus qu'à envoyer nos données dans notre vue.

Pour cela, il faut déjà que l'on dise à notre contrôleur qu'on aura besoin d'utiliser le **PetManager**. Il faut donc d'abord que tu ajoutes un `use` dans ton **PetController** avec le nom pleinement qualifié de ta classe **PetManager** (à toi de le retrouver!).

Ensuite, dans ta méthode `index()`, tu peux instancier un nouveau `PetManager`, et appeler sa méthode `selectAll()` (qu'il hérite de l'`AbstractManager`) pour récupérer tous tes animaux de compagnie dans ta base de données, et stocker le tableau résultant de cette requête dans une variable commodément nommée `$pets`. Tu peux vérifier si ta requête s'est bien passée en ajoutant `var_dump($pets);die;`.

Enfin, pour injecter tes animaux dans ta vue (ça paraît violent dit comme ça mais pas d'inquiétude, tout va bien se passer 👌), il faut que tu ajoutes un deuxième argument à ta méthode `render()`, sous la forme d'un tableau associatif. Ce tableau prend la forme suivante :

```php
[
    'nomDeLaVariableDansTwig' => $laVariableÀInjecter,
    'var2' => $var2,
    'var3' => $var3,
    ...
]
```
Ici, ce tableau n'aura qu'une ligne, puisque la seule variable que nous voulons injecter est notre tableau `$pets`.

Une fois que tu as bien ajouté ce qu'il faut dans l'appel de la méthode `render()`, tu devrais avoir accès à `$pets` dans ta vue, sous le nom que tu lui a donné pour twig (donc très probablement `pets` 🙃).

Tu peux donc faire un simple `{{ dump(pets) }}` dans twig pour vérifier que tu récupères bien tes animaux du côté de la vue. Si tu as le même résultat que lorsque tu as fais ton `var_dump($pets)` dans le contrôleur, c'est que tu récupères bien tous tes animaux, et c'est bon! Plus qu'à mettre en page à ta sauce (une petite boucle sur ton tableau sera nécessaire) pour afficher les informations de chacun de tes animaux de compagnie trop mignons! 😍

> Note : tu peux utiliser [placekitten](https://placekitten.com/) et [placepuppy](https://place-puppy.com/) pour afficher des photos de chatons ou de chiots en fonction de l'espèce de tes animaux de compagnie. 😉

#### 🏁 Conclusion

Et voilà, maintenant qu'on a vu le fonctionnement de base du Simple-MVC, à toi de jouer pour nous créer une super application pour ton projet 2! 😉