# Feedback 2

Tu t'en es bien sorti·e avec la partie des `Models` et tes méthodes `find()` et `findAll()` sont bien écrites et fonctionnent afin de récupérer les données en base de données.

Pour le reste, même si tu n'as pas implémenté toutes les fonctionnalités demandées, la base est là et c'est bien. Je te conseille de reprendre ce challenge avec la correction afin d'ajouter ce qui manque pour compléter les fonctionnalités principales. Tu peux te concentrer d'abord uniquement sur l'aspect _CRUD_ (ajout, modification, suppression) et en laissant de côté la partie _authentification_ ou les rôles.

Tu pourrais commencer par ajouter un bouton sur chaque page de listing des profs et des étudiants pour en ajouter un nouveau. Actuellement, c'est le bouton d'édition sur la page de listing des profs qui permet d'ajouter un nouveau prof. C'est un peu déroutant pour l'utilisateur.

Fais les choses pas à pas en t'aidant de la correction afin de gérer toute la partie _CRUD_ et ensuite, tu pourras t'attaquer à la partie _authentification_ puis la gestion des rôles. Le principal est de comprendre le fonctionnement et de savoir l'adapter à tes besoins.

## Models

Avant toute chose, je te conseille de regarder les `Models` de la correction car tes `Models` ne sont pas tout à fait corrects. En effet, ton model `Teacher` a bien ses attributs mais le model `Student` reprend exactement les mêmes alors qu'il devrait proposer un attribut d'ID lié à un `Teacher` et pas de `job`. Une fois cela corrigé, tu pourras ajouter un étudiant lié à un professeur (n'oublie d'adapter les `setters` et `getters`).

## Ajouter un prof

Le plus simple est l'ajout d'un professeur, ta méthode `add()` dans `TeachersController.php` permet d'afficher le formulaire, ce qui est une bonne base. Les étapes suivantes vont t'aider à ajouter ce qui manque.

### Route en POST

> Les requêtes HTTP peuvent être de plusieurs types, les plus courantes sont `GET` et `POST`. `GET` est utilisé pour récupérer des données, `POST` est utilisé pour envoyer des données. Dans le cas d'un formulaire, on utilise `POST` pour envoyer les données du formulaire.

Il faut maintenant gérer l'envoi des données en POST, donc ajouter une route en POST dans `index.php` :

```php
$router->map(
    'POST', // Méthode HTTP pour récupérer les données du formulaire
    '/teachers/add', // URL de la route, peut être la même que la route en GET
    [
        'method' => 'addPost', // Nom de la méthode du controller
        'controller' => TeachersController::class // Classe du controller
    ],
    'teacher_add_post' // Nom de la route
);
```

### La méthode addPost()

La méthode `addPost()` dans `TeachersController.php` va récupérer les données en POST. Là, tu peux t'inspirer des consignes où la méthode native PHP permet de récupérer les données en POST :

```php
public function addPost()
{
  $firstname = filter_input(INPUT_POST, 'firstname'); // Récupère la valeur du champ "firstname" du formulaire, par l'attribut "name"
  // .. et ainsi de suite pour les autres champs

  // Tu peux maintenant créer un nouveau Model et l'ajouter en DB
  $teacher = new Teacher();
  $teacher->setFirstname($firstname);
  // ... et ainsi de suite pour les autres propriétés
  $teacher->save(); // Ajoute en DB, méthode à créer dans le Model

  // Tu peux maintenant rediriger vers la page de listing des profs, de deux façons

  // 1. Retrouver tous les profs comme pour `list()`
  // Méthode simple mais redondante
  $instance = new Teacher();
  $all_teachers = $instance->findAll();

  $this->show("teacher/teacher_list", [
      'teachersResults' => $all_teachers,
  ]);

  // 2. Rediriger vers la route de la liste des profs, comme proposé dans la correction
  // Cette partie aurait davantage sa place dans le `CoreController` sous la forme d'une méthode `redirectToRoute()`
  // Je la place ici pour l'exemple mais tu peux la déplacer dans le `CoreController` si tu veux, pour pouvoir l'utiliser dans tous les controllers
  // Cette méthode permet d'appeler une route et donc d'afficher ici ce que ta méthode `list()` affiche
  global $router;

  // On génère l'URL
  $url = $router->generate('teachers-list');

  // On redirige vers cette URL
  header('Location: ' . $url);
  exit;
}
```

### La méthode save()

Tu as sans doute remarqué que la méthode `save()` n'existe pas encore dans le Model `Teacher.php`. Il faut donc l'ajouter et la remplir pour ajouter le prof en DB. La correction te propose deux choses :

- Deux méthodes en `protected` dans le modèle `Teacher.php` afin de gérer la connexion à la base de données et la requête SQL. Ces méthodes ne peuvent pas être appelées en-dehors du modèle, elles ne sont pas accessibles depuis l'extérieur. Elles sont donc appelées par la méthode `save()` qui elle est `public` et donc accessible depuis l'extérieur dans le parent `CoreModel.php`.
  - Une méthode `insert()` dans le Model `Model.php` qui permet d'ajouter un enregistrement en base de données
  - Une méthode `update()` dans le Model `Model.php` qui permet de modifier un enregistrement en base de données
- Une méthode `save()` dans le parent `CoreModel.php` qui permet d'appeler la méthode `insert()` ou `update()` en fonction de l'ID du Model. Si l'ID est `null`, c'est qu'il s'agit d'un nouvel enregistrement et donc qu'il faut appeler `insert()`, sinon, il s'agit d'une modification et donc qu'il faut appeler `update()`.

> Une méthode `public` peut être appelée depuis l'extérieur, une méthode `protected` ne peut pas être appelée depuis l'extérieur, elle est donc accessible uniquement depuis l'intérieur de la classe ou depuis les classes enfants. Et une méthode `private` ne peut être appelée que depuis l'intérieur de la classe.

Tout ceci te semble peut-être un peu complexe pour l'instant. Le plus simple dans un premier temps va être de gérer uniquement l'insertion avec une méthode `public`, directement dans le Model `Teacher.php`.

> C'est moins sécurisé, mais plus simple pour l'instant.

Je te conseille de t'inspirer de la correction et de créer la méthode `insert()` dans le Model `Teacher.php` :

```php
public function insert()
{
    // Récupération de l'objet PDO représentant la connexion à la DB
    $pdo = Database::getPDO(); // Méthode statique, donc pas besoin d'instancier la classe Database

    // Ecriture de la requête INSERT INTO
    $sql = '
        INSERT INTO `teacher` (firstname, lastname, job, status)
        VALUES (:firstname, :lastname, :job, :status)
    ';

    // Préparation de la requête d'insertion (pas exec, pas query)
    $pdoStatement = $pdo->prepare($sql);

    // On bind chaque jeton/token/placeholder
    $pdoStatement->bindValue(':firstname', $this->firstname, PDO::PARAM_STR);
    // ... et ainsi de suite pour les autres propriétés

    // On exécute la requête préparée
    $ok = $pdoStatement->execute();

    // Si au moins une ligne ajoutée
    if ($pdoStatement->rowCount() > 0) {
        // Alors on récupère l'id auto-incrémenté généré par MySQL
        $this->id = $pdo->lastInsertId();

        // On retourne VRAI car l'ajout a parfaitement fonctionné
        return true;
        // => l'interpréteur PHP sort de cette fonction car on a retourné une donnée
    }

    // Si on arrive ici, c'est que quelque chose n'a pas bien fonctionné => FAUX
    return false;
}
```

Tu peux maintenant ajouter un prof en base de données depuis ton controller ! N'hésite pas à me demander si tu as des questions.

## La suite

Tu peux mettre en place la même chose pour les étudiants, la façon de faire est la même, mis à part que les étudiants doivent être liés à un professeur. La correction te propose une solution dont tu peux t'inspirer afin de gérer la relation entre les étudiants et les professeurs.

La mise à jour est plus délicate qu'un simple ajout, car il s'agit de reprendre des données et de les insérer dans chaque champ du formulaire avec l'attribut `value`. Tu peux t'inspirer de la correction pour la mise à jour des profs et des étudiants.

## Aller plus loin

Quand ton CRUD est fonctionnel sur les profs et les étudiants, tu peux t'attaquer à la partie authentification et rôles, qui propose plus de challenge, mais la correction est là pour t'aider à poser les bases et à t'inspirer, je suis bien sûr disponible pour t'aider à comprendre la correction.

Si tu as des questions, n'hésite pas à me contacter !
