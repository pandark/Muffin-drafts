## gdb
Quelques trucs qui fonctionnent avec gdb (peut-être faisable dans lldb, mais je n'ai pas cherché)

### Visualiser les fichiers
Appuyez sur `-` pour afficher le contenu des fichiers au dessus. Attention, du coup les flèches permettent de se déplacer dans le fichier mais plus de parcourir l'historique des commandes tapées dans gdb.

### libasan
Ajouter les lignes suivantes à votre `~/.gdbinit` (créez-le s'il n'existe pas).
Elles permettent de faire un break automatiquement lorsque vous compilez avec la libasan et qu'il y a un problème. La première ligne sert juste à ce que gdb n'ignore pas la deuxième ligne.

```
set breakpoint pending on
break __asan_report_error
```

Ensuite, lorsque vous compilez avec `-fsanitize=address` et que vous faites, par exemple, une lecture ou une écriture à un endroit où vous ne devriez pas, gdb break automatiquement à cet endroit.

### up et down

Lorsque vous rencontrez un segfault ou autre, l'endroit où break gdb se situe après le problème. Pour remonter dans la pile des fonctions, utilisez `up` (ou juste `u`) pour remonter. Vous pouvez alors, par exemple, regarder le contenu des variables avec `info locals` `print ma_variable`

### déboguer ou optimiser
Utilisez l'option `-g` pour voir le contenu des fichiers correspondant à l'exécutable que vous lancez, mais n'utilisez pas l'option `-O3` qui vous empêcherait de lire le contenu de variables "optimisées".

## valgrind
Valgrind est utile pour trouver des fuites mémoires ou des erreurs d'écriture.

N'utilisez pas la libasan (`-fsanitize=address`) dans les options de compilation avant de lancer le programme avec valgrind car le code de sortie du programme ne sera pas un code d'erreurs.
