# Feedback 3

Tu as bien compris comment récupérer la liste des profs et des étudiants, avec des Models qui permettent de récupérer les données en base de données et des Controllers qui permettent de les afficher. C'est une bonne base pour la suite.

Je te conseille de reprendre la correction pour ajouter les fonctionnalités manquantes, concentre-toi sur la partie CRUD (ajout, modification, suppression) et laisse de côté la partie authentification ou les rôles dans un premier temps.

## Routes

Tu as bien compris comment fonctionnent les routes, c'est une bonne base pour la suite. Il y seulement une petite erreur à la base, pour ta toute première route, tu as mis en place cela :

```php
$router->map(
    'GET',
    '/home',
    [
        'method' => 'home',
        'controller' => MainController::class // On indique le FQCN de la classe
    ],
    'main-home'
);
```

Ce qui fait, que la route `/` n'existe pas, tu pourrais juste changer le chemin de la route pour que ça fonctionne :

```php
$router->map(
    'GET',
    '/',
    [
        'method' => 'home',
        'controller' => MainController::class
    ],
    'main-home'
);
```

Mais ton message d'erreur permet bien de retour à l'accueil dans tous les cas, ce qui est bien (il faudra adapter le lien après cette modification).

## Views

Tu as bien repris le code fourni pour la partie HTML, c'est bien, mais tu répètes à chaque fois toute la partie `head` ou la navbar, ce qui est difficile à maintenir si on ajoute un lien dans la navbar par exemple.

Tu as une solution toute simple pour arranger ça, tu peux créer un fichier `header.tpl.php` et `footer.tpl.php` dans un dossier `layout` par exemple, et tu peux les inclure dans tes views depuis ton `MainController.php` dans ta méthode `show()`.

```php
require_once __DIR__ . '/../views/layout/header.tpl.php';
require_once __DIR__ . '/../views/' . $viewName . '.tpl.php';
require_once __DIR__ . '/../views/layout/footer.tpl.php';
```

Ainsi, tu n'as plus besoin de répéter le code HTML à chaque fois, et si tu veux ajouter un lien dans la navbar, tu n'as qu'à le faire dans le fichier `header.tpl.php`. Et chaque view n'a plus qu'à s'occuper de son contenu. Sur plusieurs pages, il n'y actuellement pas de navbar, cela permettra de mettre la même sur toutes les pages.

## Ajout d'un prof

Je te conseille dans un premier temps, de t'occuper de l'ajout d'un prof. C'est une route et une méthode que tu as commencé à implémenter, mais il manque encore quelques éléments.

> Tu pourrais commencer par ajouter un étudiant, mais comme il y a un lien avec les profs, cela rajoute une difficulté supplémentaire.

Tu as bien compris comment récupérer les données dans une route POST, c'est une bonne base. Tu n'as plus qu'à créer une méthode `insert()` dans ton Model `Teacher.php` en t'inspirant de celle que tu as faite dans `Student.php`.

Comme tu as déjà créé une view pour ajouter un prof, tu n'auras plus qu'à modifier la route du bouton Ajouter dans la view de la liste des profs afin de rediriger vers la bonne route. Et cette route, il faudra la créer ainsi que la méthode associée dans le controller `TeacherController.php`.

Ta route actuelle :

```php
$router->map(
    'GET||POST',
    '/teacher/add',
    [
        'method' => 'teacherAdd',
        'controller' => TeacherController::class // On indique le FQCN de la classe
    ],
    'teacher_add'
);
```

En tant que telle, elle fonctionnera, mais afin de la simplifier, tu pourrais en faire deux, une en `GET` et une en `POST` :

```php
$router->map(
    'GET',
    '/teacher/add',
    [
        'method' => 'add',
        'controller' => TeacherController::class
    ],
    'teacher_add'
);

$router->map(
    'POST',
    '/teacher/add',
    [
        'method' => 'addPost',
        'controller' => TeacherController::class
    ],
    'teacher_add_post'
);
```

C'est bien ce vouloir faire une route qui fonctionne en `GET` et en `POST`, mais cela peut être plus compliqué à maintenir. Cela dépend des cas, mais dans ton cas, tu peux faire deux routes, une en `GET` et une en `POST`.

Tu vas pouvoir créer une méthode `add()` et une méthode `addPost()` dans ton controller `TeacherController.php`. Dans la méthode `add()`, tu vas pouvoir afficher le formulaire d'ajout d'un prof, et dans la méthode `addPost()`, tu vas pouvoir récupérer les données du formulaire et les envoyer dans la méthode `insert()` de ton Model `Teacher.php`.

La méthode `addPost()` est proche de celle qui existe déjà `studentAdd()`, tu peux simplement renommer la méthode et t'assurer que la fonction marche.

## Supprimer un prof

La suppression est plus simple à gérer que la gestion d'un formulaire. Tu vas devoir :

- créer une route
- une méthode `delete()` dans ton controller `TeacherController.php` pour gérer la suppression
- une méthode `find()` dans ton Model `Teacher.php` pour récupérer un prof en fonction de son ID
- une méthode `delete()` dans ton Model `Teacher.php` pour supprimer un prof en fonction de son ID

La route prendra un paramètre, celui de l'ID du prof à supprimer :

```php
$router->map(
    'GET',
    '/teachers/[i:id]/delete', // `id` est le nom du paramètre et `i` indique que c'est un nombre
    [
        'method' => 'delete', // On indique la méthode du controller à appeler
        'controller' => TeacherController::class
    ],
    'teacher_delete' // On donne un nom à la route
);
```

La méthode dans le controller permet de récupérer l'ID du prof à supprimer et d'appeler la méthode `find()` du Model `Teacher.php` puis la méthode `delete()` du Model `Teacher.php`.

- Pour la méthode `find()` et `delete()`, celles de la correction devrait t'aider

```php
public function delete($id)
{
    // On commence par récupérer le Model Teacher
    $teacher = Teacher::find($id);

    // Alors on peut supprimer
    $teacher->delete();

    // On redirige vers la liste des profs (n'hésite pas à t'inspirer de la correction)
}
```

Ensuite dans la liste des profs, tu pourras ajouter la route comme ceci :

```php
<a class="dropdown-item" href="<?= $router->generate('teacher_delete', [ "id" => $currentTeacher->getId() ]) ?>">
Oui, je veux supprimer
</a>
```

`teacher_delete` est bien le nom de la route et le paramètre `id` est inséré en tant que paramètre dans la route.

## Aller plus loin

Tu peux t'inspirer de la correction pour mettre en place l'ajout d'un étudiant, puis la modification d'un prof ou d'un étudiant. Je suis disponible si tu as des questions !

> Le model `Student` n'a actuellement pas la relation `teacher_id` avec le model `Teacher`, tu peux t'inspirer de la correction pour l'ajouter.

Une fois que tout cela sera fonctionnel, je te conseille de regarder la correction pour la partie authentification afin de la mettre en place dans ton projet. Mais souviens-toi de te concentrer d'abord sur la partie CRUD, c'est le plus important et tu n'es pas loin d'avoir un projet totalement fonctionnel.

Si tu as des questions, n'hésite pas à me contacter !
