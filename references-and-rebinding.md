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

## Arguments and Arrays {#arrays}

JavaScript fournit deux types différents de conteneurs pour les valeurs. Nous en avons déjà rencontré un, le tableau. Voyons comment il traite les valeurs et les identités. Pour commencer, nous allons apprendre à extraire une valeur d'un tableau. Nous allons commencer par une fonction qui crée une nouvelle valeur avec une identité unique à chaque fois que nous l'appelons. Nous savons déjà que chaque fonction que nous créons est unique, c'est donc ce que nous utiliserons:

    var unique = function () {
                    return function () {}
                  };
    
      unique()
        //=> [Function]
        
      unique() === unique()
        //=> false

Vérifions que ce que nous avons dit sur les références s'applique aussi bien aux fonctions qu'aux tableaux:

      var x = unique(),
          y = x;
          
      x === y
        //=> true

D'accord. Alors qu'en est-il des choses * à l'intérieur * des tableaux? Nous savons comment créer un tableau avec quelque chose à l'intérieur:

      [ unique() ]
        //=> [ [Function] ]

C'est un tableau avec l'une de nos fonctions uniques. Comment en tirer quelque chose en *retour* ?

      var a = [ 'hello' ];
      
      a[0]
        //=> 'hello'
        
Cool, les tableaux fonctionnent beaucoup comme des tableaux dans d'autres langues et sont basés sur zéro. Le problème avec cet exemple est que les chaînes sont des types de valeur en JavaScript, donc nous n'avons aucune idée si `a [0]` nous donne toujours la même valeur que la recherche d'un nom dans un environnement, ou si cela fait de la magie qui essaie pour nous donner une nouvelle valeur.

Nous devons mettre un type de référence dans un tableau. Si nous récupérons la même chose, nous savons que le tableau stocke une référence à tout ce que vous y mettez. Si vous récupérez quelque chose de différent, vous savez que les tableaux stockent des copies des choses.[^hunh]

[^hunh]: Les tableaux dans tous les langages contemporains stockent des références et non des copies, nous pouvons donc être pardonnés de nous attendre à ce qu'ils fonctionnent de la même manière en JavaScript. Néanmoins, c'est un exercice utile pour tester les choses par nous-mêmes.

Testons-le:

    var unique = function () {
                    return function () {}
                  },
        x = unique(),
        a = [ x ];
        
    a[0] === x
      //=> true

Si nous obtenons une valeur d'un tableau en utilisant le suffixe `[]`, c'est exactement la même valeur avec la même identité. Question: Cela s'applique-t-il à d'autres emplacements de la baie? Oui:

    var unique = function () {
                   return function () {}
                 },
        x = unique(),
        y = unique(),
        z = unique(),
        a = [ x, y, z ];
        
    a[0] === x && a[1] === y && a[2] === z
      //=> true
