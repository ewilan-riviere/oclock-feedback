# MCD

J'ai bien relu ton MCD et je te propose quelques retours ci-dessous.

Plusieurs éléments sont bons dans ton MCD, il y a cependant quelques erreurs à corriger. Tout d'abord les entités doivent être au pluriel, par exemple, `USER` deviendra `USERS`, les clés primaires de chaque entité doivent être également soulignées afin de les différencier des autres attributs. Les associations doivent être des circulaires dans leur représentation schématique, pour les différencier des entités. Tu as bien mis des verbes pour les associations, c'est une bonne chose. La relation entre les `USERS` et les `PRODUCTS` est bien une relation Many to Many, mais ta façon de l'indiquer n'est pas la bonne. Les cardinalités que tu as placées devraient être `0, N` <=> `0, N` selon la logique d'un utilisateur qui peut liker aucun ou plusieurs produits, et un produit qui peut être liké par aucun ou plusieurs utilisateurs.

Le fait que l'entité `ADDRESS` ait une relation One to One avec `USERS` est bonne, tout comme le fait que les `USERS` puissent avoir plusieurs `ADDRESS`. Pour la relation entre les `USERS` et les `ORDERS`, c'est également bon avec une relation asymétrique comme la précédente. Et pour la relation spécifique entre `ORDERS` et `PRODUCTS`, tu as bien indiqué que les `ORDERS` doivent contenir au moins un `PRODUCTS`, ce qui est une bonne chose.

Tes attributs sont bons et l'on peut se questionner juste pour `total_amount` de l'entité `ORDERS` qui pourrait être calculé à partir des `PRODUCTS` et de leur prix, c'est là un choix de conception et les deux sont possibles, en particulier en cas de promotion par exemple ta solution est meilleure, bien que cet aspect ne soit pas demandé dans le projet.

C'est presque parfait, corrige juste les erreurs que j'ai mentionnées et tu pourras passer à la conception de la base de données 😄

## Tutoriels

Tu trouveras ci-dessous une liste non exhaustive de tutoriels sur Merise.

- [Grafikart](https://grafikart.fr/tutoriels/merise) : tutoriels vidéo sur Merise (mon préféré)
- [Cours de modélisation](http://cours.pise.info/modelisation/merise.htm) : cours sur la modélisation de données
- [Developez.com](https://ineumann.developpez.com/tutoriels/merise/initiation-merise/) : cours sur Merise

## Logiciels de MCD

Tu trouveras ci-dessous une liste non exhaustive de logiciels de MCD. Certains sont payants, d'autres gratuits, certains sont en ligne, d'autres à télécharger. À toi de choisir celui qui te convient le mieux !

- [AnalyseSI](https://framalibre.org/content/analysesi) : open source, disponible pour Windows, Mac et Linux (mon préféré)
- [OpenModelSphere](https://sgbd.developpez.com/telecharger/detail/id/2758/Open-Modelsphere) : open source, prend en charge la notation Merise, Windows et Linux
- [Dia](http://dia-installer.de/) : open source, disponible pour Windows, Mac et Linux
- [DBDesigner](https://www.dbdesigner.net/) : disponible en ligne, avec un plan gratuit
- [NextObjects](https://theleadingedge.developpez.com/dno/) : open source, disponible pour Windows et Linux
- [Lucidchart](https://www.lucidchart.com/pages/) : disponible en ligne, avec un plan gratuit
- [Draw.io](https://app.diagrams.net/) : disponible en ligne, avec un plan gratuit
- [PowerAMC](https://www.lesnumeriques.com/telecharger/sap-powerdesigner-poweramc-20522) : avec une version d'essai, uniquement Windows
