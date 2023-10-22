# Feedback 4

Actuellement ton challenge ne fonctionne pas directement, notamment à cause de l'absence du `MainController.php` que tu appelles à plusieurs endroits ou le `CoreController.php`, peut-être que tu as oublié de les ajouter dans ton commit ?

Quoi qu'il en soit, tu as voulu répondre à toutes les demandes et c'est tout à fait louable. Cependant, tu as sûrement trop vite récupéré les méthodes des projets précédents sans regarder comment elles marchaient en détails. Et c'est compréhensible que tu ais fait ça, vu que tu as beaucoup de choses à faire en peu de temps. Il aurait été plus pratique de récupérer les méthodes une par une et de les adapter pour qu'elles fonctionnent dans ton projet.

C'est dommage parce que tu as beaucoup d'éléments positifs dans **tes views qui semblent très bien construites** ainsi que **tes models qui sont très complets**. Le seul problème est qu'ils ne fonctionnent pas ensemble, en particulier via les controllers.

La correction va t'aider à voir ce qui manque, mais avant tout, je te conseille de reprendre tes controllers depuis le début. L'idée est de remettre le `CoreController.php` et le `MainController.php` qui devraient être présents dans ton commit. Ensuite, tu peux reprendre les méthodes une par une en les testant à chaque fois pour voir si elles fonctionnent.

Une fois que tu auras un retour dans ton navigateur, tu pourras travailler plus facilement pour faire fonctionner tes controllers. Le plus important est de te concentrer sur une fonctionnalité à la fois : si l'ajout ne fonctionne pas, ne t'en fais pas pour la modification, la suppression ou la validation des données. Il faut que tu te concentres sur une seule chose à la fois, tu verras que ce sera plus facile.

Pour tes routes, je te conseille de séparer à chaque fois `GET` et `POST` afin de rendre cela plus simple et d'éviter `GET||POST` pour l'instant. De même pour l'authentification, laisse-là de côté pour l'instant et essaye de faire fonctionner tes routes sans cela. Tu pourras y revenir après.

Ne t'inquiète pas, il y avait beaucoup de choses à gérer donc c'est normal que tu aies eu du mal à tout faire. Mais je suis sûre que tu vas y arriver, il faut juste que tu prennes le temps de bien comprendre comment fonctionnent les controllers et les routes.

Regarde bien ton fichier `index.php` qui contient les routes :

```php
$router->map(
    'GET',
    '/',
    [
        'method' => 'home',
        'controller' => MainController::class // On indique le FQCN de la classe
    ],
    'main-home'
);
```

Cette route est très bien faite, mais elle appelle le controller `MainController.php` qui n'était pas ton commit, elle tombe donc dans le vide et l'application plante, il faut juste que tu ajoutes ce fichier dans ton commit ou s'il n'existe pas, que tu le crées avec la méthode `home()` comme indiqué dans la route. La route s'appelle `main-home` et elle est accessible via l'URL `/` en méthode `GET`. Tu pourras alors utiliser la méthode `show()` du `CoreController.php` (qu'il va falloir ajouter également) que tu peux là récupérer d'un projet précédent pour afficher la view `main/home.tpl.php`.

Par exemple, tu peux faire cela :

```php
<?php

namespace App\Controllers;

class MainController extends CoreController
{
    public function home()
    {
        $this->show('main/home');
    }
}
```

Essaye tout d'abord d'afficher cela dans ton navigateur et après tu pourras passer à la suite, le listing des profs et des étudiants. Le `CoreController.php` de la correction peut t'aider mais veille à ne prendre que les méthodes dont tu as besoin pour l'instant.

Si tu préfères qu'on le fasse ensemble au début, je suis disponible. Regarde bien la correction et si tu es perdu·e, on peut explorer ensemble ton code pour voir ce qui ne va pas.

Une fois que tu auras un retour sur ton navigateur avec un listing des profs et des étudiants, essaye de mettre en place l'ajout puis la modification, mais seulement après avoir réussi à afficher les données. C'est une bonne base pour la suite, reprends chaque fichier et ne garde que ce qui est nécessaire pour l'instant. Sinon tu auras trop d'informations à gérer et tu risques de t'y perdre.

Bon courage !

Si tu as des questions, n'hésite pas à me contacter !
