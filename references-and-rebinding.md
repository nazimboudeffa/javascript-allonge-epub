# The Pause That Refreshes: Rebinding and References {#references}

![It is not enough that coffee taste beautiful. Everything about its creation and consumption should reflect coffee's beauty.](images/bezzera.jpg)

### Une question simple

Considerez ce code:

    var x = 'June 14, 1962',
        y = x;
        
    x === y
      //=> true

Cela a un sens évident, car nous savons que les chaînes sont un type valeur, donc quelle que soit l'expression que vous utilisez pour dériver la valeur '14 juin 1962', vous allez obtenir une chaîne avec exactement la même identité.

Mais qu'en est-il de ce code?

    var x = [2012, 6, 14],
        y = x;
        
    x === y
      //=> true

C'est également vrai, même si nous savons que chaque fois que nous évaluons une expression telle que `[2012, 6, 14]`, nous obtenons un nouveau tableau avec une nouvelle identité. Alors, que se passe-t-il dans nos environnements?

### arguments et références

Dans notre discussion sur [closures] (#closures), nous avons dit que les environnements lient des valeurs (comme `[2012, 6, 14]`) à des noms (comme `x` et` y`), et que lorsque nous utilisons ces noms en tant qu'expressions, le nom est évalué comme valeur.

What this means is that when we write something like `y = x`, the name `x` is looked up in the current environment, and its value is a specific array that was created when the expression `[2012, 6, 14]` was first evaluated. We then bind *that exact same value* to the name `y` in a new environment, and thus `x` and `y` are both bound to the exact same value, which is identical to itself.

Cela signifie que lorsque nous écrivons quelque chose comme `y = x`, le nom «x» est recherché dans l'environnement actuel, et sa valeur est un tableau spécifique qui a été créé lorsque l'expression `[2012, 6, 14]` a d'abord été évalué. Nous lions alors *cette même valeur exacte* au nom `y` dans un nouvel environnement, et ainsi `x` et `y` sont tous deux liés à la même valeur exacte, qui est identique à elle-même.

La même chose se produit avec la liaison d'une variable via un moyen plus conventionnel d'appliquer une fonction aux arguments :

    var x = [2012, 6, 14];
    
    (function (y) {
      return x === y
    })(x)
      //=> true

`x` et `y` les deux finissent par être liés exactement au même tableau, et non à deux tableaux différents qui se ressemblent à nos yeux.
