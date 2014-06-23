**Si vous voyez des erreurs ou des améliorations à apporter, n'hésitez pas à me le signaler [sur le forum de l'intra](https://intra.42.fr/forum/#!/ALGO-1-001/Muffin-Drafts-sur-les-pointeurs-arguments-listes-arbres-etc-Lisez-et-participez).**

## Prérequis

Je vous conseil fortement de vous assurer que vous avez bien compris le fonctionnement [des pointeurs, des arguments de fonctions, des allocations de mémoire statique et dynamique](http://muffin.lambdaweb.fr/#/Drafts/read/22) avant de lire à ce draft.

Note : Le code donné en exemple n'est pas à la norme et il peut manquer certains éléments pour qu'il fonctionne correctement (fonction main et headers par exemple). Il est là pour faire comprendre les notions.

## Structure d'une liste chainée

Une liste chainée est composée de maillons. Le premier maillon est parfois appelé ancre (anchor). Ces maillons sont des structures contenant un ou des pointeurs vers d'autres structures. Les maillons peuvent également contenir une ou plusieurs autres variables de n'importe quels types.

* Ci-dessous, deux exemples de structures de maillons pour créer une liste simplement chainée. Les deux contiennent un pointeur _next_ sur l'élément suivant de la liste. Le premier contient une variable _value_ de type `int` et une variable _letter_ de type `char`, le deuxième contient une variable _content_ de type `void *`.
 * Le pointeur _next_ du dernier élément de la liste pointe sur `NULL` si la liste n'est pas circulaire, ou sur le premier élément si elle l'est (lui-même s'il n'y en a qu'un).

``` c
struct              s_list
{
    int             value;
    char                letter;
    struct s_list       *next;
};
```
``` c
struct              s_list
{
    void                *content;
    struct s_list       *next;
};
```

* Liste doublement chainée contenant une variable _content_ de type `void *`, un pointeur _next_ sur l'élément suivant de la liste et un pointeur _prev_ sur l'élément précédent de la liste.
 * Le pointeur _next_ du dernier élément de la liste pointe sur `NULL` si la liste n'est pas circulaire, ou sur le premier élément si elle l'est (lui-même s'il n'y en a qu'un).
 * Le pointeur _prev_ du premier élément de la liste pointe sur `NULL` si la liste n'est pas circulaire, ou sur le dernier élément si elle l'est (lui-même s'il n'y en a qu'un).

``` c
struct              s_dlist
{
    void                *content;
    struct s_list       *prev;
    struct s_list       *next;
};
```

## Création de listes chainées

### Liste chainée non circulaire avec une allocation statique de mémoire

On crée les maillons du dernier au premier pour avoir leur adresse à assigner comme valeur au pointeur `next` du maillon précédent à chaque fois) :

``` c
struct s_list       l3 = {"3", NULL};
struct s_list       l2 = {"2", &l3};
struct s_list       l1 = {"1", &l2};
struct s_list       l0 = {"0", &l1};
```

Les maillons étant déclarés de manière statique, on donne leur adresse en utilisant `&`.

### Liste chainée non circulaire avec une allocation dynamique de mémoire

#### En un seule fonction, non modulaire

``` c
struct s_list       *l0;
struct s_list       *l1;
struct s_list       *l2;
struct s_list       *l3;

l0 = (struct s_list *)malloc(sizeof(*l0));
l1 = (struct s_list *)malloc(sizeof(*l1));
l2 = (struct s_list *)malloc(sizeof(*l2));
l3 = (struct s_list *)malloc(sizeof(*l3));
if (l0 != NULL && l1 != NULL && l2 != NULL && l3 != NULL)
{
    l0.content = "0";
    l1.content = "1";
    l2.content = "2";
    l3.content = "3";
    l0->next = l1;
    l1->next = l2;
    l2->next = l3;
    l3->next = NULL;
}
…
free(l0);
free(l1);
free(l2);
free(l3);
```

#### En rendant le code modulaire
``` c
struct s_list       *new_list(void *content)
{
    struct s_list       *new;

    if ((new = (struct s_list *)malloc(sizeof(*new))) == NULL)
        return (NULL);
    new->content = content;
    new->next = NULL;
    return (new);
}
```
``` c
void    list_push(struct s_list **list, struct s_list *new)
{
    struct s_list       *cur;

    if (*list == NULL)
        *list = new;
    else
    {
        cur = *list;
        while (cur->next != NULL)
            cur = cur->next;
        cur->next = new;
    }
}
```
``` c
void    list_del_all(struct s_list **list)
{
    struct s_list       *next;

    while (*list != NULL)
    {
        next = (*list)->next;
        free(*list);
        *list = next;
    }
}
```
``` c
int             i;
struct s_list       *list;
struct s_list       *new;
const char      *content[4] = {"0", "1", "2", "3"};

i = 0;
while (i < 4)
{
    new = new_list(content[i]);
    if (new != NULL)
        list_push(&list, new);
    else
    {
        write(2, "no memory available :-(\n", 24);
        list_del_all(&list);
        return (1);
    }
    ++i;
}
…
list_del_all(&list);
return (0);
```

## Pile et file
On parle de pile (_stack_ ou FILO pour first in last out) et de file (_queue_ ou FIFO pour first in first out) pour désigner deux utilisations des liste chainées.
* Lorsqu'on ajoute les nouveaux éléments à la fin de la liste et qu'on retire les éléments également à la fin de la liste, on parle de pile. Si, au contraire, on ajoute les éléments au début de la liste et qu'on les retire à la fin de la liste, on parlera de file.
* On appelle généralement _push_ la fonction pour ajouter un élément à la fin de la liste, et _pop_ celle pour en retirer depuis la fin de la liste. On appelle généralement _shift_ la fonction pour retirer un élément au début de la liste et _unshift_ la fonctions pour ajouter un élément au début de la liste.

``` c
struct s_list       *list_pop(struct s_list **list)
{
    struct s_list       *cur;
    struct s_list       *next;

    cur = next = *list;
    if (*list == NULL || *list->next == NULL)
    {
        *list = NULL;
        return (cur);
    }
    while (next->next != NULL)
    {
        cur = next;
        next = next->next;
    }
    cur->next = NULL;
    return (next);
}
```
``` c
struct s_list       *list_shift(struct s_list **list)
{
    struct s_list       *anchor;

    if (*list == NULL)
        return (NULL);
    anchor = *list;
    *list = (*list)->next;
    return (anchor);
}
```
``` c
void        list_unshift(struct s_list **list, struct s_list *new)
{
    if (*list == NULL)
        *list = new;
    else
    {
        new->next = *list;
        *list = new;
    }
}
```

## Arbres

Les listes chainées permettent de se déplacer dans deux sens mais dans une seule direction : on peut avancer avec le pointeur _next_ et reculer avec le pointeur _prev_. Les arbres reposent sur les mêmes principes que les listes chainées mais en permettant de se déplacer dans plusieurs directions.

Pour les arbres, on parle de nœud plutôt que de maillon. Le premier nœud est appelé racine (root).

### Arbres binaires

Les arbres binaires ont un pointeur _parent_ qui permet de remonter vers la racine de l'arbre, un pointeur _left_ qui permet de descendre vers la gauche et un pointeur _right_ qui permet de descendre vers la droite.

Dans un arbre binaire de recherche, la valeur des enfants doit être supérieur à celle du nœud, et la valeur de l'enfant de gauche doit être inférieur à celle de l'enfant de droite.

``` c
struct              s_btree
{
    void                *content;
    struct s_list       *parent;
    struct s_list       *left;
    struct s_list       *right;
};
```

#### Parcours

Il est possible de parcourir (récursivement) les arbres binaires de plusieurs manières : **préfixe** (current, left, right), **infixe** (left, current, right) et **postfixe/suffixe** (left, right, current).

``` c
void    btree_apply_prefix(struct s_btree *tree, void (*fun)(struct s_btree *node))
{
    fun(tree);
    if (tree->left != NULL)
        btree_apply_prefix(tree->left)
    if (tree->right != NULL)
        btree_apply_prefix(tree->right)
}
```
La fonction _btree_apply_prefix_ ci-dessus applique la fonction _fun_ sur chacun des nœuds de l'arbre parcouru selon l'ordre préfixe.

#### Équilibrage

Un arbre binaire est dit équilibré si pour n'importe quel nœud, il y a au plus une différence de 1 entre le nombre de nœuds enfants (récursivement) à gauche et à droite.

Il existe plusieurs techniques pour équilibrer un arbre. Certaines s'appliquent au moment de l'insertion, d'autres à n'importe quel moment.

La recherche dans un arbre équilibré est en règle générale plus rapide, mais l'équilibrage d'un arbre peut prendre du temps et des ressources. Certaines des techniques d'équilibrage nécessitent une structure particulière des maillons, comme les arbres bicolores pour lesquels il faut une variable indiquant la couleur de chaque nœud.

Quelques exemples :
* [Arbre bicolore / rouge et noir](http://fr.wikipedia.org/wiki/Arbre_bicolore)
* [Arbre AVL](http://fr.wikipedia.org/wiki/Arbre_AVL)
* […](http://en.wikipedia.org/wiki/Self-balancing_binary_search_tree#Implementations)

### Arbres à trois enfants ou plus

Chaque nœud a zéro, un ou plusieurs enfants (nombre limité par la structure des nœuds) qui eux même ont des enfants, et ainsi de suite. Pour un arbre ternaire, on peut imaginer un pointeur _parent_, un pointeur _left_, un pointeur _middle_ et un pointeur _right_. On peut aussi simplement appeler les pointeurs vers les enfants _child1_, _child2_, etc.

``` c
struct              s_ttree
{
    void                *content;
    struct s_list       *parent;
    struct s_list       *left;
    struct s_list       *middle;
    struct s_list       *right;
};
```

* [Beaucoup de types d'arbres différents](http://en.wikipedia.org/wiki/List_of_graph_theory_topics#Trees)
