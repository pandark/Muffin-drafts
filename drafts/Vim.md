Voilà quelques trucs que ceux qui choisissent d'utiliser le meilleur éditeur de texte à 42 pourront trouver intéressants (oui, on parle de Vim, c'est écrit dans le titre !).

Vous n'aurez pas forcément besoin de tout ; à vous de piocher ce qui vous semble utile.

Note : le signe `^` suivi d'un autre signe servira la plupart du temps à indiquer que l'on appuie en même temps sur la touche `ctrl` et sur la touche correspondant au deuxième signe. Par exemple `^x` correspond à ctrl+x.

### vimtutor
Ouvrez un terminal, écrivez `vimtutor` puis validez avec la touche entrée. Vous pouvez maintenant suivre les instructions du tutoriel (en anglais) pour apprendre les bases de Vim.

### Les modes
Il y a plusieurs __modes__ dans vim, dont principalement
* __Normal__, pour se déplacer rapidement et passer dans les autres modes (on y revient en tapant sur la __touche échap__)
* __Insertion__, pour écrire du texte (on y arrive en tapant `i`, `a`, `I`, `A`, `o`, `O`, `s` ou `S` en mode normal)
* __Ligne de commande__, pour taper des commandes à exécuter (on y arrive en tapant `:` en mode normal)
* __Visuel__, pour sélectionner visuellement du texte (on y arrive en tapant `v`, `V` ou `^V` en mode normal)
* __Remplacement__, pour écraser le texte avec du nouveau texte (on y arrive en tapant `R` en mode normal, ou `r` pour un seul caractère)

### Les déplacements
__Lorsque vous avez compris le principe du déplacement, vous pouvez l'appliquer dans à peu près tous les modes sauf l'insertion (d'où l'intérêt de sortir du mode insertion dès que vous avez fini d'écrire)__, pour la suppression, la copie, la sélection, l'indentation, la mise en majuscule, le rot13, etc.

En gros, on peut se déplacer dans les quatre directions (avec les flèches ou `h``j``k``l`), mais aussi, et c'est bien plus intéressant, par mot, par paragraphe, à l'intérieur ou à l'extérieur de paires de signes (parenthèses, accolades, chevrons…), vers une ligne dont on connait le numéro, etc.

Une fois que vous avez eu un aperçu de tout ça via vimtutor, vous voudrez sans doute pouvoir expliquer aux emacsiens que vous croisez que __votre éditeur à vous, il roxx du poney par pack de douze__ (et accessoirement être plus efficaces et avoir quelques aides visuelles lorsque vous éditez votre code).

### Les aides visuelles

#### Coloration syntaxique
La commande `:syntax on` active la coloration syntaxique. Bon, les couleurs de base piquent les yeux mais il y a déjà un gros mieux.

`:nohl` pour désactiver la surbrillance le résultat de votre dernière recherche (jusqu'à ce que vous en fassiez une nouvelle). Il peut être intéressant de le remapper, par exemple sur `<space>/`.

#### 80ème colonne
L'entête, c'est bien gentil mais il est tout en haut du fichier. La commande `:set colorcolumn=80` permet de colorer la colonne indiquée (ici la 80ème). Par défaut, c'est en rouge vif, mais en cherchant un peu ça peut s'arranger assez facilement.

#### Numérotation des lignes
Afficher les numéros des lignes `:set number` ou relativement à la ligne actuelle `:set relativenumber` (`:set rn` ou `:set rel` puis un appui sur la touche tab pour que vim complète la commande ^_-) pour inverser ces commandes, ajoutez `no` devant ou un point d'exclamation derrière (`:set nonumber` pour ne pas les afficher, et `:set number!` pour inverser le comportement actuel). La numérotation relative est particulièrement pratique pour vérifier à combien de lignes on en est dans une fonction en se plaçant sur la première accolade, mais peut être source de distraction si on la laisse activée tout le temps.

### Commandes et raccourcis utiles :
* `fn + F1` pour ajouter les entêtes 42 à vos fichiers.
* `d` pour couper, `y` pour copier, `p` pour coller après le curseur, ou `P` pour coller avant le curseur.
* `i` pour passer en mode insertion au niveau du curseur, `a` pour après le curseur, `I` au début de la ligne, `A` à la fin de la ligne, `o` sur une nouvelle ligne après, `O` sur une nouvelle ligne avant.
* `G` pour aller à une ligne (par exemple `42G` pour aller à la ligne 42), `G` tout seul pour aller à la dernière ligne, et `gg` pour aller à la première ligne.
* `=` pour réindenter automatiquement (par exemple `gg=G` aller à la première ligne et réindenter jusqu'à la dernière). Il y a deux cas pour lesquels l'indentation par défaut de Vim et celle de la norme ne sont pas en accord : les commentaires (vim met une espace puis une astérisque alors que la norme veut deux astérisques sans espace avant), et les conditions sur plusieurs lignes pour les structures de contrôle (vim rajoute deux tabs alors que la norme n'en veut qu'un).
* `v` permet de faire une sélection caractère par caractère, mais il existe aussi `V` pour la sélection ligne par ligne, et même `^v` pour la sélection rectangulaire !
* `gU` pour mettre en majuscule, `gu` pour mettre en minuscule (par exemple j'ai pas mal utilisé `gUaW` pour mettre les noms des #define en majuscules).
* `:w` pour enregistrer, `:wq` ou `ZZ` pour quitter en enregistrant, `:q!` ou `ZQ` pour quitter sans enregistrer
* `^l` pour rafraichir l'affichage (pratique quand des messages de déconnexion/reconnexion du nfs s'affichent ou qu'on a redimensionné la fenêtre du terminal)
* `/` (par exemple /toto pour trouver toto, rototo et totoro) pour faire une recherche (ça fonctionne aussi dans les pages de man) ou `*` pour faire une recherche exacte sur le mot sur lequel est placé le curseur, puis n pour aller à l'occurrence suivante correspondant au motif de la recherche ou N pour aller à la précédente.
* `u` pour défaire votre dernière action (undo), et `^r` pour refaire une action que vous avez défaite (on peut annuler et refaire plusieurs actions, par exemple `42u` `42^r` défait puis refait les 42 dernières actions).
* `.` permet de refaire la dernière action que l'on a fait (rien à voir avec undo et redo cette fois), par exemple si je suis passé en mode insertion pour écrire toto puis que je suis sorti du mode insertion et que j'appuie sur `.`, un nouveau toto s'écrit (ce qui donnera probablement tottotoo).
* `gf` pour ouvrir le fichier sur le nom duquel se trouve le curseur
* `K`, `2K`, `3K` pour ouvrir la page (1, 2 ou 3) de man correspondant au mot sur lequel se trouve le curseur (sinon, vous pouvez aussi installer un des plugins qui permettent d'ouvrir les pages de man directement dans vim).
* `^a` et `^x` permettent d'incrémenter et décrémenter un nombre qui se trouverait sur la ligne (attention, ils suppriment les zéros qui se trouvent avant le nombre).
* `:s/oompa/loompa/g` remplace toutes les occurrences de *oompa* par *loompa* dans la ligne `:%s/you lost/the game/g` remplace toutes les occurrences de *you lost* par *the game* dans toutes les lignes (sans le g à la fin, seul la première occurrence de la ligne ou des lignes est remplacée).

### Copier/coller en dehors de vim
* Lorsque vous voulez coller depuis l'extérieur dans vim (avec commande+v) sans que les lignes soient réindentées automatiquement, utilisez la commande `:set paste`, puis `:set nopaste` pour réactiver l'indentation automatique une fois que vous avez terminé.

### La souris
Personnellement, je ne trouve pas ça pratique, mais il est possible d'activer la souris dans vim
* `:set mouse=a` pour l'activer
* `:set mouse=` pour la désactiver (et donc l'utiliser normalement, comme avec n'importe quel autre programme)

### L'autocomplétion :
En mode insertion, lorsque vous avez commencé à écrire un mot, vous pouvez tapper `^n` (contrôle + n) pour faire apparaitre les suggestions de complétion pour ce mot, puis `^n` et `^p` pour aller respectivement à la suggestion précédente ou suivante. Pour les noms de fichiers, c'est `^x ^f` (contrôle + x, puis contrôle + f).

### Ouvrir des fichiers et des buffers
* `:e repertoire/nomdufichier.ext` ouvre un fichier depuis le disque dur (sans utiliser un éventuel buffer déjà existant)
* `:b repertoire/nomdufichier.ext` ouvre le buffer correspondant au fichier (s'il est déjà ouvert, il n'est pas rechargé depuis le disque)
* `:n` pour aller au buffer ouvert suivant, et `:N` ou `:prev` pour aller au précédant (`:first` ou `:rewind` pour le premier et `:last` pour le dernier).
* `:b1`, `:b2` pour aller au premier, deuxième, etc. buffer ou `:b#` pour aller au buffer ouvert avant celui en cours

### Les splits
* horizontal : `:split ft_vous_avez_perdu.c` ou `^w s` ou `:snew`
* vertical : `:vplit ft_the_game.c` ou `^w v` ou `:vnew`
(sans nom de fichier indiqué, Vim split avec le fichier déjà ouvert dans les deux panels, vous pouvez utiliser `:e` pour ouvrir un autre fichier ensuite)
* `vim -o fichier1 fichier2` pour ouvrir des fichiers en split horizontal
* `vim -O fichier1 fichier2` pour ouvrir des fichiers en split vertical
* Pour passer d'un panel à l'autre, `^w` puis, au choix, `←↓↑→` ou `hjkl` (pour aller au prochain panel dans la direction indiquée), ou `^w` (pour aller au prochain panel).

Pour effectuer une rotation entre deux panels, `^w r`. Pour modifier la taille des panel `^w` puis `>` ou `<` ou `-` ou `+` (suivant si c'est un split vertical ou horizontal), et `^w =` pour les mettre à la même taille.

### Les tabs :
* `vim -p fichier1 fichier2 fichier3` (max 10 tabs par défaut)
* `:tabnew` pour ouvrir une nouvelle tab
* `:tabn` et `:tabp` pour se déplacer entre les onglets

### Remapping de touches
Il est possible (et même assez simple) d'associer une, plusieurs, ou une suite de touches à une commande ou à une d'autres touches.
* Par exemple la commande `:inoremap kj <Esc>` vous permettra de tapper les touches `k` puis `j` en mode insertion (pour revenir en mode normal) au lieu de la touche échap (difficile d'accès). Vous pouvez l'ajouter à votre fichier de configuration, c'est bien pratique ;).

### Les plugins
Pour installer des plugins plus simplement : [Pathogen](https://github.com/tpope/vim-pathogen#installation) Si vous voulez plus d'options (et de complications diront certains) il existe des alternatives comme [NéoVundle](https://github.com/Shougo/neobundle.vim).

#### Suggestions de plugins :
* [Préremplissage des fichiers C à la norme 42](https://github.com/anonkey/vim_bundles.git) [voir forum intra](https://intra.42.fr/forum/#!/General/Remplissage-automatique-fichiers-vim)
* [FSwitch](http://www.vim.org/scripts/script.php?script_id=2590), pour passer rapidement des fichiers .c aux fichiers .h et inversement
* Ajout de surround (s) aux mouvements inside (i) et arround (a) (par exemple pour remplacer des guillemets par des chevrons) : [surround.vim](https://github.com/tpope/vim-surround)
* Pour utiliser des linters dans vim (utilise make pour les fichiers C par défaut) : [syntastic.vim](https://github.com/scrooloose/syntastic)
* Pour afficher les marques, les lignes modifiées, etc dans la gouttière à gauche : [quickfixsigns.vim](https://github.com/tomtom/quickfixsigns_vim)
* Utiliser git dans vim : [fugitive.vim](https://github.com/tpope/vim-fugitive)
* Une meilleur gestion du `%` : [python_match.vim](https://github.com/vim-scripts/python_match.vim)
* Une meilleur gestion du . : [repeat.vim](https://github.com/tpope/vim-repeat)
* Complétion de snipets prédéfinis à la Textmate : [snipmate.vim](https://github.com/msanders/snipmate.vim)
* __Man.vim__ pour utiliser la commande `:Man` qui affiche une page de man dans une fenêtre splittée. Pour activer le ftplugin man, il suffit de sourcer le fichier du plugin correspondant `/usr/share/vim/vim73/ftplugin/man.vim`.
 
##### Sourcer un fichier
Comment source-t-on un fichier dans vim ? avec la commande `:so`.

Par exemple, pour sourcer le fichier `/usr/share/vim/vim73/ftplugin/man.vim`, ajoutez à votre fichier `~/.vimrc` (ou `~/.myvimrc` s'il est lui-même sourcé depuis ~/.vimrc) la ligne suivante :

`so /usr/share/vim/vim73/ftplugin/man.vim`

Vous remarquerez qu'il n'y a pas de `:` au début ; ils ne sont en effet pas nécessaires dans les fichiers de configuration.
Si vous n'avez pas de fichier `~/.vimrc`, il suffit d'en créer un.

### Thèmes
Les thèmes sont des plugins particuliers qui servent à définir les couleurs utilisées par vim (et/ou gvim)
* [kalahari](https://github.com/fabi1cazenave/kalahari.vim) est celui que j'utilise (dérivé de [desert](https://github.com/fugalh/desert.vim))
* [Solarized](https://github.com/altercation/vim-colors-solarized)
* [Molokai](https://github.com/tomasr/molokai)

### Vimrc
Il est d'usage de partager son fichier de configuration (.vimrc) si possible commenté afin que les autres puissent y piocher des astuces intéressantes.
* [Voilà le mien](https://github.com/pandark/dotfiles/blob/42/.vimrc)
* [Celui de mblet](http://pastebin.com/QqE7Fivd) [voir forum intra](https://intra.42.fr/forum/#!/ALGO-1-001/Config-de-VIM)
* Celui d'[aviala](https://github.com/darnuria/my_dotfiles/blob/master/.vimrc)

### S'entrainer et progresser
#### En ligne
* Vimtutor
* [Vim adventure](http://vim-adventures.com/)
* [Vimgolf](http://vimgolf.com/)

#### IRL
* [TupperVim](http://wiki.mozfr.org/TupperVim)

### Distributions vim
Si vous préférez partir d'un vim avec des plugins préinstallés et préconfigurés (plus rapide mais moins personnalisé), il existe plusieurs _distributions_ vim. Pensez à vérifier qu'elles sont à jour ; vous ne voudriez pas vous retrouver avec des plugins obsolètes. Voilà quelques exemples :
* [Janus](https://github.com/carlhuda/janus) [voir forum intra](https://intra.42.fr/forum/#!/General/Distribution-Vim-Janus)
* [Subvim](https://github.com/reversTeam/Sublivim) [voir forum intra](https://intra.42.fr/forum/#!/General/Sublivim-V-0)

Voilà voilà, ça fait déjà pas mal, je pense, sachant que vous avez bien d'autres choses pour vous occuper. :)
Il reste plein de choses, macros, marques, inside, arround… mais ça fait déjà pas mal pour l'instant.

Si vous avez besoin d'aide, `:help!` et si vous êtes toujours bloqué après ça, essayez sans le point d'exclamation :D

Et allez, un petit truc inutile pour la route : `?g` permet de faire un rot13 (par exemple `gg?gG` pour le faire de la première à la dernière ligne du buffer).

Pour les suggestions d'ajouts et/ou de modifications : [utilisez ce thread du forum de l'intra](https://intra.42.fr/forum/#!/General/Vim).
