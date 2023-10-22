# Feedback 1

Tu es allé·e très loin sur ce challenge, félicitations ! Tu as très bien mené toutes les fonctionnalités de base qui étaient demandées, mais également presque toute la partie bonus, c'est excellent !

Il y a quelques éléments qui ne fonctionnent pas exactement (et ça ne concerne que le bonus) comme tu le souhaites, mais rien de grave, tu as très bien compris le fonctionnement de `AltoRouter` et tu as su mettre en place les routes, les controllers et les views. C'est très bien !

Si tu souhaites mieux comprendre ce qui s'est passé, tu peux lire les explications ci-dessous.

## Routes d'édition

Tu as bien mis en place les routes d'édition pour les profs et les étudiants, mais il y a un petit souci. Ton formulaire se présente comme ceci dans `teacher_update.tpl.php` (le problème est le même pour les étudiants) :

```php
<form action="<?= $router->generate('teacher_update_post') ?>" method="POST" class="mt-5">
```

La route générée ici est `/teacher` et non `/teacher/{id}`. C'est parce que tu n'as pas passé l'id du professeur dans la route. Il faut donc que tu passes l'id du professeur dans la route. Tu peux le faire en passant l'id dans l'URL :

```php
<form action="<?= $router->generate('teacher_update_post', ['teacherid' => $teacher->getId()]) ?>" method="POST" class="mt-5">
```

Pour vérifier la route qui est générée la meilleure solution est de l'afficher sur la page concernée, tu peux ajouter ceci dans le fichier `teacher_update.tpl.php` (temporairement) :

```php
<?= $router->generate('teacher_update_post', ['teacherid' => $teacher->getId()]) ?>
```

Et tu verras que la route générée est bien `/teacher/1` par exemple, sachant que le `1` est l'id du professeur.

### Pourquoi ?

Afin de savoir comment utiliser la méthode `generate()` du router, il faut regarder la documentation de la bibliothèque `AltoRouter`. Tu peux retrouver la dépendance dans le fichier `composer.json`, celle-ci se nomme `altorouter/altorouter`, et une petite recherche sur Internet plus tard, tu pourras trouver sa page GitHub : <https://github.com/dannyvankooten/AltoRouter>. La documentation des bibliothèques se trouve généralement sur la page GitHub du projet ou sur un site dédié, accessible depuis la page GitHub.

> Selon ton IDE, tu pourras avoir une autocomplétion plus ou moins efficace. Ainsi, en mettant ta souris sur la méthode `generate()` du router, tu pourras voir la documentation de la méthode dans le controller. Tu peux aussi utiliser la fonction `var_dump()` pour afficher le résultat de la méthode. parmi les IDE les plus utilisés, tu as VSCode, PHPStorm, Sublime Text, Atom, etc. Des extensions existent pour mieux gérer le PHP dans ces IDE, dans mon cas j'utilise VSCode avec l'extension [PHP Intelephense](https://marketplace.visualstudio.com/items?itemName=bmewburn.vscode-intelephense-client).

Dans l'exemple en haut du `README.md`, tu verras comment utiliser la méthode `generate()` du router. Tu peux voir que la méthode prend deux paramètres :

- le nom de la route, sous forme de chaîne de caractères
- les paramètres de la route, sous forme de tableau associatif

Dans l'exemple, on passe un paramètre `id` à la route `user-details`: `echo $router->generate('user-details', ['id' => 5]);`.

Ce qui donnera pour nous :

```php
echo $router->generate('teacher_update_post', ['teacherid' => $teacher->getId()]); // Output: "/teacher/1"
```

### Le nom du paramètre

Dans notre cas, le nom du paramètre est `teacherid` et non `id`. C'est parce que dans le fichier `index.php`, où tu as défini tes routes, tu as défini le paramètre comme ceci :

```php
$router->map(
    'POST',
    '/teacher/[i:teacherid]', // Le nom du paramètre est teacherid
    [
        'method' => 'teacherUpdatePost',
        'controller' => TeacherController::class
    ],
    'teacher_update_post'
);
```

### Une autre solution

Une autre solution, que tu pourras trouver dans la correction, est de laisser `AltoRouter` matcher automatiquement la route selon l'URL courante. Pour cela, il suffit de laisser `action` vide dans le formulaire :

```php
<form action="" method="POST" class="mt-5">
```

Et de laisser `AltoRouter` matcher la route comme tu le fais à la fin de ton fichier `index.php` :

```php
$match = $router->match();
```

### La route d'édition des étudiants

La route d'édition des étudiants, mis à part la question de l'id, est bien mise en place. Il y a juste un petit souci pour le formulaire de sélection des professeurs, tu as bien récupéré la liste et généré les options, mais tu as oublié de mettre l'option sélectionnée. Pour cela, il faut ajouter l'attribut `selected` à l'option qui correspond au professeur de l'étudiant :

```php
<option value="<?= $currentTeacher->getId() ?>"<?php if ($currentTeacher->getId() == $student->getTeacherId()) : ?> selected<?php endif ?>><?= $currentTeacher->getFirstname() ?> <?= $currentTeacher->getLastname() ?> - <?= $currentTeacher->getJob() ?></option>
```

C'est un attribut spécial pour le tag HTML `option` qui permet de sélectionner une option par défaut. Pour en savoir plus regarde la [documentation mdn](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option) ou n'hésites pas à consulter la correction !

## Les routes de suppression

Lorsqu'on utilise le bouton de suppression, cela nous amène sur la liste des profs avec le prof qui a bien été supprimé, c'est presque ça ! Il faut juste que tu rediriges vers la liste des profs après la suppression, pour éviter l'ajout de `/delete` dans l'URL.

Pour rediriger vers une URL, le mieux c'est de créer une nouvelle méthode dans le `CoreController.php`, la correction te propose cette méthode qui fonctionne très bien :

```php
protected function redirectToRoute(string $routeName, $routeParams = [])
{
    // global, toujours moche, mais pour l'instant, on sait pas faire autrement
    global $router;

    // On génère l'URL
    $url = $router->generate($routeName, $routeParams);

    // On redirige vers cette URL
    header('Location: ' . $url);
    exit;
}
```

Et tu peux l'utiliser dans tes controllers comme ceci :

```php
$this->redirectToRoute('teacher-list');
```

## Aller plus loin

Tu pourrais mettre en place une seule page pour l'édition et la création avec la gestion des données afin de choisir si c'est pour la création ou l'édition, la correction te propose une solution intéressante à mettre en place pour éviter de dupliquer ton code et donc de le maintenir à deux endroits différents.

Si tu as le temps, regarde la correction pour le CSRF, c'est une notion très intéressante et c'est utile de savoir comment le mettre en place.

Encore bravo pour ton travail !

Si tu as des questions, n'hésite pas à me contacter !
