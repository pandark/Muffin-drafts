**Si vous voyez des erreurs ou des améliorations à apporter, n'hésitez pas à me le signaler [sur le forum de l'intra](https://intra.42.fr/forum/#!/ALGO-1-001/Muffin-Drafts-sur-les-pointeurs-arguments-listes-arbres-etc-Lisez-et-participez).**

## Variables

* Une variable est un **espace mémoire réservé** permettant de stocker une **valeur**.
* La **taille** de cet espace et la **manière d'encoder cette valeur** sont définis par le **type** de la variable.
 * exemple de tailles : un octet (8 bits) pour un `char` ou un `unsigned char`, 4 octets pour un `int` ou un `unsigned int`.
* L'encodage de la valeur utilise un **bit de signe** (le premier bit) pour indiquer le signe à moins que la variable soit de type non-signé (**unsigned**). Dans ce dernier cas, elle peut donc stocker une valeur **deux fois plus grande** grâce à ce bit.
 * signé : 1000001 = -1, 0000001 =1
 * non-signé : 1000001 = 129, 0000001 = 1

## Pointeurs

* **Les pointeurs sont des variables contenant une adresse mémoire**.
* Le type de ces variables est `uintptr_t` qui est, sur OSX, un `typedef` de `unsigned long` défini dans `stdint.h`. Sa valeur maximale est `UINTPTR_MAX`.
* L'adresse `NULL` (0) définie dans `stdlib.h` correspond à un emplacement où il n'est jamais possible d'allouer de la mémoire, et auquel il est donc impossible de lire ou d'écrire.

## Allocation de mémoire dynamique ou statique

Il est possible d'allouer de la mémoire de deux manières : statiquement (sur la pile) ou dynamiquement (sur le tas).

### Allocation statique

* L'allocation **statique** se fait sur la **pile d’exécution**.
* La mémoire alloué de manière statique est **automatiquement libérée à la fin de la fonction** dans laquelle est déclarée la variable correspondante (à la fin du programme pour les globales). La pile ayant une taille limitée, si on alloue **trop de mémoire statiquement** (dans un même fonction ou dans des fonctions qui s'appellent), il peut se produire un dépassement de la pile d'exécution (**stack overflow**).
* Si l'on indique sa taille dans la déclaration d'un tableau, c'est une allocation statique de mémoire.
 * Exemple : `char  alpha[26];`

Note : il ne faut pas confondre l'allocation statique et le mot clé `static` qui n'a pas grand chose à voir.

### Allocation dynamique

* L'allocation **dynamique** se fait sur le **tas** (heap).
* La mémoire alloué de manière dynamique **doit être libérée par le programmeur** (en utilisant la fonction `free`). Il y a peu de risques d'atteindre la taille maximale du tas, car lorsque la mémoire vive est remplie on utilise de la mémoire stockée sur le disque dur (swap), qui a cependant le désavantage de demander plus de temps pour y accéder.
* Pour allouer de la mémoire, on indique **un pointeur** auquel sera affecté **comme valeur l'emplacement de l'espace mémoire réservé**, et la **taille de mémoire souhaitée**. Si l'allocation de mémoire échoue, la valeur du pointeur est mise à `NULL`.
 *Exemple :

``` c
#include <stdlib.h>
…
char        *alpha;
alpha = (char *)malloc(sizeof(*alpha) * 26);
if (alpha == NULL)
    // Something went wrong :(
else
{
…
    free(alpha);
}
```

Notes :
* Dans ce cas précis, on pourrait retirer le sizeof(*alpha) car la taille d'un char est 1 octet (on fait donc une multiplication par 1).
* On peut faire l'assignation et le test en une seule ligne : `if ((alpha = (char *)malloc(sizeof(*alpha) * 26)) == NULL)`
* Utiliser `free` sur un pointeur dont la valeur est `NULL` ne renvoie pas d'erreur. En revanche, utiliser cette fonction sur un pointeur dont la valeur désigne un emplacement où il n'y a pas (ou plus, si on a déjà utilisé `free` sur ce pointeur) de mémoire réservée en causera une. **Attention, donc, aux double `free`**.
* **Si la mémoire n'est pas libérée, on parle de fuite mémoire**. Le système d'exploitation peut essayer d'y parer (surtout si la valeur des pointeurs n'a pas été modifiée) mais c'est dans tous les cas une **mauvaise pratique**. **Chaque fois que vous allouez de la mémoire, pensez à la libérer**.

## Passage des arguments de fonctions par copie ou par référence

Lorsque l'on passe une variable en argument à une fonction, une copie est créée.

* Cette nouvelle variable est locale à la fonction, elle n'existe que le temps pendant lequel la fonction est appelée.
* Modifier cette copie ne modifie pas l'originale.

Ainsi, si on veut modifier la valeur d'une variable, il faut passer une référence à cette variable à l'aide d'un pointeur. Une copie de ce pointeur sera crée.

* Cette copie pointera sur la variable originale, permettant ainsi de modifier sa valeur.
* Modifier ce pointeur ne modifiera pas un autre pointeur sur la variable originale. Pour cela, selon le même principe, il faut envoyer à la fonction un pointeur sur pointeur.

## Structures

* Les structures permettent de regrouper des variables de même type ou de types différents.

``` c
struct                  s_car
{
    int                 nb_doors;
    char                    *model_name;
    struct s_person     *driver;
    void                    (* drive)(struct s_car *car, int speed);
};
```

Dans l'exemple ci-dessus,  on voit une structure de type `struct s_car` contenant un entier_nb_doors_, une chaine de caractères _model_name_, un pointeur sur une structure `s_person` _driver_ et un pointeur sur une fonction _drive_ de type `void` prenant en argument un pointeur sur structure `s_car` _car_ et un entier _speed_.

* Pour accéder à une variable à l'intérieur d'une structure, on utilise le signe `.` (`structure.variable`).
* Si on utilise un pointeur sur une structure et non directement la structure, on peut utiliser `->` (`pointeur_sur_structure->variable` qui équivaut à `(*pointeur_sur_structure).variable`).

## Arguments de fonctions

### Pointeurs et tableaux

Pour passer un tableau en argument à une fonction, on passe le pointeur qui sert à l'identifier. C'est **suffisant pour modifier les valeurs** contenues dans les cases de ce tableau.

**Si l'on veut modifier la taille du tableau** (ce qui n'est possible que si sa mémoire a été déclarée dynamiquement), il faut modifier le pointeur pour le faire pointer sur un nouvel espace mémoire (et éventuellement copier le contenu actuel du tableau) ; il faut donc passer **un pointeur sur ce pointeur** à la fonction, comme expliqué précédemment.

### Structures

L'une des utilité des pointeurs est de pouvoir regrouper plusieurs variables de même type dans des tableaux. Les structures sont utilisées elles aussi pour regrouper des variables. On serait donc tenté de penser que les structures et les tableaux fonctionnent plus ou moins de la même manière. Pourtant, ce n'est pas tout à fait le cas.

En ce qui concerne le passage en paramètre aux fonctions, **les structures sont considérées comme de simples variables**. Passer une structure reviendra donc à passer **une copie de cette structure**, ce qui n'est généralement pas ce que vous souhaitez. Il faudra donc, si vous voulez la modifier dans cette fonction, **passer un pointeur sur la structure**.
