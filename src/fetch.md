# Fetch en JavaScript

Dans le web, les sites communiquent beaucoup avec d'autres sites web ou des serveurs. Généralement ces données sont sous forme de JSON pour des API[^1] modernes ou de XML pour des API plus anciennes, mais elles peuvent prendre des formes bien différentes selon les cas.

En JavaScript, il existe une fonctionnalité moderne qui permet de récupérer des données depuis une URL : `fetch()`. C'est une méthode native du navigateur, c'est-à-dire qu'elle est disponible dans tous les navigateurs récents.

Pendant longtemps, on a utilisé la fonction `XMLHttpRequest()` pour récupérer des données depuis une URL. Mais son implémentation est assez complexe et lourde alors que `fetch()` est beaucoup plus simple à utiliser et est disponible depuis ECMAScript[^2] 2015, aussi appelé ES6. En bref, c'est historique et l'apparition de `fetch()` a permis de simplifier les choses.

La documentation MDN de `fetch()` est disponible [ici](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) sur tu souhaites en savoir plus. La suite ici, traitera de l'utilisation de `fetch()`.

## Exemples

### XMLHttpRequest (ancienne méthode)

Certains vieux projets utilisent encore `XMLHttpRequest()`, mais il est préférable d'utiliser `fetch()`.

```js
const req = new XMLHttpRequest();
req.responseType = "json";
const url = "https://jsonplaceholder.typicode.com/posts";
req.open("GET", url, true);
req.onload = function () {
  const jsonResponse = req.response;
  console.log(jsonResponse);
};
req.send();
```

### Fetch (méthode moderne)

Bien plus moderne et facile à utiliser, `fetch()` est devenu très populaire.

```js
const url = "https://jsonplaceholder.typicode.com/posts";
fetch(url)
  .then((response) => response.json())
  .then((data) => console.log(data));
```

## Utilisation

Comme `fetch()` appelle un serveur distant, la méthode est asynchrone, c'est-à-dire qu'elle ne bloque pas l'exécution du code, car le serveur peut mettre plusieurs millisecondes à répondre.

> En PHP, une requête HTTP est synchrone, c'est-à-dire qu'elle bloque l'exécution du code jusqu'à ce que le serveur ait répondu. C'est pour cela que le JavaScript est très utilisé pour les requêtes HTTP, car il permet de ne pas bloquer l'exécution du code.

Elle retourne une _promesse_ qui sera résolue lorsque les données seront récupérées. Il y a deux manières de récupérer les données :

- en utilisant `.then()` qui permet d'ajouter une fonction qui sera appelée lorsque la promesse sera résolue, l'exemple précédent utilise cette méthode
- en utilisant `await` qui permet d'attendre que la promesse soit résolue

```js
const response = await fetch("https://jsonplaceholder.typicode.com/posts");
const data = await response.json();
```

> Ici, `json()` est une méthode de l'objet `response` qui permet de récupérer les données au format JSON. Il existe d'autres méthodes comme `text()` ou `blob()` qui permettent de récupérer les données dans d'autres formats.

La deuxième méthode est plus récente et plus facile à utiliser, mais elle n'est pas disponible partout. Dans les frameworks modernes, on utilise `await`, mais dans du code JavaScript directement interprété par le navigateur, on ne peut pas utiliser `await` à la racine du code, voir la section [Depuis un navigateur](#depuis-un-navigateur), on utilise alors `.then()`. Cela est également valable pour des projets plus anciens qui ne proposent pas `await` (on parle de top-level `await`).

> Attention
>
> `await` ne peut être utilisé que dans une fonction asynchrone. Pour utiliser `await`, il faut donc utiliser `async` devant la fonction.
>
> ```js
> async function getData() {
>   const response = await fetch("https://jsonplaceholder.typicode.com/posts");
>   const data = await response.json();
>   console.log(data);
>   return data;
> }
> ```

Il faut savoir qu'une méthode asynchrone retourne une promesse. On peut donc utiliser `.then()` pour récupérer les données ou `await` si on est dans une fonction asynchrone.

```js
getData().then((data) => console.log(data)); // avec .then()
const data = await getData(); // avec await
console.log(data);
```

`await` permet de simplifier le code et de le rendre plus lisible. C'est pour cela qu'il est préférable de l'utiliser si c'est possible.

## Depuis un navigateur

Le navigateur ne permet pas d'utiliser `await` à la racine du code, il faut donc utiliser `.then()`. Mais il est possible d'utiliser `await` dans une fonction asynchrone.

```html
<script>
  // CECI NE FONCTIONNERA PAS DANS UN NAVIGATEUR
  const response = await fetch("https://jsonplaceholder.typicode.com/posts");
  const data = await response.json();
  console.log(data);
</script>
```

Pour utiliser `await`, il faut donc utiliser une fonction asynchrone.

```html
<script>
  // async permet de définir une fonction asynchrone
  async function getData() {
    // await fonctionne car on est dans une fonction asynchrone
    const response = await fetch("https://jsonplaceholder.typicode.com/posts");
    const data = await response.json();
    console.log(data);
    return data;
  }

  let apiData = [];
  // à la racine du code, on ne peut pas utiliser await dans un navigateur
  getData().then((data) => {
    apiData = data;
    console.log(apiData);
  });
</script>
```

- Dans la fonction `getData()`, `await` est utilisé et il n'y a pas de `.then()`, car on est dans une fonction asynchrone.
- La fonction `getData()` est appelée à la fin du code pour lancer la récupération des données, mais sans bloquer l'exécution du code.
- On ne met pas `await` devant `getData()`, car on est dans le code principal (à la racine du code) et on ne peut pas utiliser `await` à la racine du code dans un navigateur.

## Depuis Node.js

Avec Node.js, la méthode `fetch()` n'était pas disponible jusqu'à récemment, il fallait donc utiliser une librairie externe comme [node-fetch](https://github.com/node-fetch/node-fetch) qui permettait d'utiliser `fetch()` depuis `node-fetch` dans Node.js. D'autres librairies comme [axios](https://github.com/axios/axios) pouvaient être utilisées également pour ajouter cette fonctionnalité.

Mais depuis Node.js v18, `fetch()` est disponible nativement, il n'est donc plus nécessaire d'utiliser une librairie externe. Néanmoins, dans des projets plus anciens, il est possible de retrouver `node-fetch` ou `axios`. Et dans certains projets, pour des questions de rétrocompatibilité ou d'options plus avancées, il est possible de retrouver ces libraries.

## En savoir plus

### Voir ses requêtes HTTP

Dans les navigateurs modernes, il est possible de voir les requêtes HTTP qui sont faites depuis le navigateur. Il suffit d'aller dans l'onglet _Réseau_ ou _Network_ de la console de développement, tu pourras voir toutes les requêtes HTTP qui sont faites depuis le navigateur, c'est très pratique pour débugger.

### Axios

Dans des projets plus complexes, tu pourras retrouver une librairie comme [axios](https://github.com/axios/axios) qui permet de faire des requêtes HTTP plus facilement. Tu peux faire la même chose avec `fetch()`, `axios` permet simplement d'ajouter des fonctionnalités supplémentaires et c'est historiquement une librairie très utilisée. Il faut ajouter que `axios` est disponible dans Node.js et dans les navigateurs, ce qui n'est le cas de `fetch()` que depuis Node.js v18.

### Synchrone et asynchrone

Les fonctions asynchrones ne bloquent donc pas l'exécution du code, comme dans cet exemple :

```js
let items = [];
fetch("https://jsonplaceholder.typicode.com/posts")
  .then((response) => response.json())
  .then((data) => {
    items = data;
    console.log(items);
  });

console.log("Je suis exécuté avant la fin de la requête HTTP");
console.log(items); // Sera vide
```

Dans cet exemple, la requête HTTP est asynchrone, c'est pour cela que la variable `items` est vide, car la requête n'est pas encore terminée. Elle se termine dans le second `.then()`.

Mais il est possible de contrôler l'asynchronicité avec `async` et `await`. Dans l'exemple suivant, la requête HTTP est toujours asynchrone, mais le code est exécuté de manière synchrone, car `await` permet d'attendre que la requête soit terminée avant de continuer l'exécution du code.

```js
let items = [];
async function getData() {
  const response = await fetch("https://jsonplaceholder.typicode.com/posts");
  const data = await response.json();
  console.log("Je suis exécuté après la fin de la requête HTTP");
  items = data;
  console.log(items);
}
getData();
```

Ici, la requête HTTP est toujours asynchrone, mais le code est exécuté de manière synchrone, car `await` permet d'attendre que la requête soit terminée avant de continuer l'exécution du code.

---

[^1]: API ou _Application Programming Interface_, sous ce nom un peu sibyllin se cache une interface qui permet de récupérer des données depuis un serveur. C'est un peu comme une base de données, mais qui est accessible depuis n'importe quel site web. C'est très pratique pour récupérer des données sans avoir à les stocker sur son propre serveur. On parle alors d'API web, mais le terme _API_ désigne aussi la manière dont on communique avec n'importe quelle application. Par exemple, utiliser jQuery c'est communiquer avec l'API de jQuery pour réaliser différentes actions, et ce n'est pas une API web.
[^2]: ECMAScript, également abrégé en ES, c'est une spécification de langage de programmation qui définit les fonctionnalités, la syntaxe et les conventions pour le langage JavaScript. En d'autres termes, ECMAScript est la norme sur laquelle le langage JavaScript est basé. JavaScript est le nom couramment utilisé pour désigner le langage de programmation basé sur ECMAScript.
