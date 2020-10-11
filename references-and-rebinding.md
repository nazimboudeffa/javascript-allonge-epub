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

## References and Objects {#objects}

JavaScript fournit également des objets. Le mot «objet» est chargé dans les cercles de programmation, en raison de l'utilisation répandue du terme «programmation orientée objet» qui a été inventé par Alan Kay mais qui depuis a fini par signifier beaucoup, beaucoup de choses pour de nombreuses personnes différentes.

In JavaScript, un objet[^pojo] est une map des noms aux valeurs, un peu comme un environnement. La syntaxe la plus courante pour créer un objet est simple:

[^pojo]: La tradition voudrait que nous appelions des objets qui ne contiennent aucune fonction "POJOs," c'est-à-dire des objets JavaScript simples.

    { year: 2012, month: 6, day: 14 }
    
Deux objets créés de cette manière ont des identités différentes, tout comme des tableaux:

    { year: 2012, month: 6, day: 14 } === { year: 2012, month: 6, day: 14 }
      //=> false

Les objets utilisent `[]` pour accéder aux valeurs par nom, en utilisant une chaîne:

    { year: 2012, month: 6, day: 14 }['day']
      //=> 14

Les valeurs contenues dans un objet fonctionnent comme les valeurs contenues dans un tableau:

    var unique = function () {
                    return function () {}
                  },
        x = unique(),
        y = unique(),
        z = unique(),
        o = { a: x, b: y, c: z };

    o['a'] === x && o['b'] === y && o['c'] === z
      //=> true

Les noms n'ont pas besoin d'être des chaînes alphanumériques. Pour toute autre chose, mettez l'étiquette entre guillemets:

    { 'first name': 'reginald', 'last name': 'lewis' }['first name']
      //=> 'reginald'

Si le nom est une chaîne alphanumérique conforme aux mêmes règles que les noms de variables, il existe une syntaxe simplifiée pour accéder aux valeurs:

    { year: 2012, month: 6, day: 14 }['day'] ===
        { year: 2012, month: 6, day: 14 }.day
      //=> true

Tous les conteneurs peuvent contenir n'importe quelle valeur, y compris des fonctions ou d'autres conteneurs:

    var Mathematics = {
      abs: function (a) {
             return a < 0 ? -a : a
           }
    };

    Mathematics.abs(-5)
      //=> 5

Funny we should mention `Mathematics`. If you recall, JavaScript provides a global environment that contains some existing objects that have handy functions you can use. One of them is called `Math`, and it contains functions for `abs`, `max`, `min`, and many others. Since it is always available, you can use it in any environment provided you don't shadow `Math`.

C'est drôle de mentionner `Mathématiques`. Si vous vous souvenez bien, JavaScript fournit un environnement global qui contient des objets existants dotés de fonctions pratiques que vous pouvez utiliser. L'un d'eux s'appelle `Math`, et il contient des fonctions pour `abs`,` max`, `min` et bien d'autres. Comme il est toujours disponible, vous pouvez l'utiliser dans n'importe quel environnement à condition de ne pas observer `Math`.

    Math.abs(-5)
      //=> 5
## Reassignment and Mutation {#reassignment}

Comme la plupart des langages de programmation impératifs, JavaScript vous permet de réaffecter la valeur des variables. La syntaxe est familière aux utilisateurs des langages les plus courants:

    var age = 49;
    age = 50;
    age
      //=> 50
      
Nous avons pris le temps d'examiner attentivement ce qui se passe avec les liaisons dans les environnements. Prenons le temps d'explorer ce qui se passe avec la réaffectation de valeurs à des variables. La clé est de comprendre que nous relions une valeur différente au même nom dans le même environnement.

Considérons donc ce qui se passe avec une variable ombrée:

    (function () {
      var age = 49;
      (function () {
        var age = 50;
      })();
      return age;
    })()
      //=> 49

Lier `50` au vieillissement dans l'environnement intérieur ne change pas `l'âge` dans l'environnement extérieur parce que la liaison de `l'âge` dans l'environnement intérieur fait de l'ombre à la liaison de `l'âge` dans l'environnement extérieur. On passe de:

    {age: 49, '..': global-environment}

Vers:

    {age: 50, '..': {age: 49, '..': global-environment}}

Et retours vers:

    {age: 49, '..': global-environment}

{pagebreak}    
Cependant, si nous n'observons pas `age` en utilisant explicitement `var`, le réaffecter dans un environnement imbriqué modifie l'original:

    (function () {
      var age = 49;
      (function () {
        age = 50;
      })();
      return age;
    })()
      //=> 50

Comme pour évaluer les étiquettes de variables, lorsqu'une liaison est rebondie, JavaScript recherche la liaison dans l'environnement actuel, puis chaque ancêtre à son tour jusqu'à ce qu'il en trouve un. Il relie ensuite le nom dans cet environnement.

![Cupping Grinds](images/cupping.jpg)

### mutation and aliases

Maintenant que nous pouvons réattribuer des choses, il y a un autre facteur important à considérer: certaines valeurs peuvent *muter*. Leurs identités restent les mêmes, mais pas leur structure. Plus précisément, les tableaux et les objets peuvent muter. Rappelez-vous que vous pouvez accéder à une valeur depuis un tableau ou un objet en utilisant `[]`. Vous pouvez également réaffecter une valeur en utilisant `[]`:

    var oneTwoThree = [1, 2, 3];
    oneTwoThree[0] = 'one';
    oneTwoThree
      //=> [ 'one', 2, 3 ]

Vous pouvez même ajouter une valeur:

    var oneTwoThree = [1, 2, 3];
    oneTwoThree[3] = 'four';
    oneTwoThree
      //=> [ 1, 2, 3, 'four' ]

Vous pouvez faire la même chose avec les deux syntaxes pour accéder aux objets:

    var name = {firstName: 'Leonard', lastName: 'Braithwaite'};
    name.middleName = 'Austin'
    name
      //=> { firstName: 'Leonard',
      #     lastName: 'Braithwaite',
      #     middleName: 'Austin' }

`Halloween` et `allHallowsEve` sont tous deux liés à la même valeur de tableau dans l'environnement local. Et aussi:

    var allHallowsEve = [2012, 10, 31];
    (function (halloween) {
      // ...
    })(allHallowsEve);
    
Il existe deux environnements imbriqués et chacun lie un nom à la même valeur de tableau. Dans chacun de ces exemples, nous avons créé deux *alias* pour la même valeur. Avant de pouvoir réaffecter les choses, le point le plus important à ce sujet est que les identités étaient les mêmes, car elles avaient la même valeur.

Ceci est vital. Considérez ce que nous savons déjà sur l'observation:

    var allHallowsEve = [2012, 10, 31];
    (function (halloween) {
      halloween = [2013, 10, 31];
    })(allHallowsEve);
    allHallowsEve
      //=> [2012, 10, 31]

La valeur externe de `allHallowsEve` n'a pas été modifiée car tout ce que nous avons fait était de relier le nom «halloween» dans l'environnement interne. Cependant, que se passe-t-il si nous *mutons* la valeur dans l'environnement interne?

    var allHallowsEve = [2012, 10, 31];
    (function (halloween) {
      halloween[0] = 2013;
    })(allHallowsEve);
    allHallowsEve
      //=> [2013, 10, 31]
      
Ceci est différent. Nous n'avons pas rebondi le nom interne vers une variable différente, nous avons muté la valeur que les deux liaisons partagent. Maintenant que nous avons terminé avec la mutation et les alias, jetons un coup d'œil.

T> JavaScript permet la réaffectation de nouvelles valeurs à des liaisons existantes, ainsi que la réaffectation et l'affectation de nouvelles valeurs à des éléments de conteneurs tels que des tableaux et des objets. La mutation d'objets existants a des implications spéciales lorsque deux liaisons sont des alias de même valeur.
