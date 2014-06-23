**Note : Ce draft n'est pas tout à fait complet et peut comporter des erreurs. N'hésitez pas à me les signaler.**

[![http://www.xkcd.com/538/](http://imgs.xkcd.com/comics/security.png "Actual actual reality: nobody cares about his secrets.  (Also, I would be hard-pressed to find that wrench for $5.)")](http://www.xkcd.com/538/)

# Un peu de théorie

On va commencer par les bases théoriques. Si vous êtes pressé et que vous voulez juste créer vos clés… pas de bol, ça risque quand même de prendre un peu de temps, mais vous pouvez quand même passer directement à la partie pratique (à vos risques et périls).

## Le chiffrement asymétrique
Le chiffrement asymétrique à base de paire de clés sert à authentifier ou à chiffrer des informations. Ces algorithmes ont l'avantage de reposer sur le secret de la clé privée et non sur le secret de la méthode de chiffrement (qui sont généralement publiques). Leur efficacité peut être modulée selon la longueur de clé utilisée.

Si vous voulez en savoir plus, je vous conseille [l'article sur Wikipedia](http://fr.wikipedia.org/wiki/Cryptographie_asym%C3%A9trique), et si vous êtes curieux, [AES encryption explained in Stick figures](http://www.moserware.com/2009/09/stick-figure-guide-to-advanced.html).

## Les clé PGP
Les clés GPG vont par paire : une clé privée et une clé publique. Elles sont utilisées pour faire du chiffrement asymétrique. La clé publique est diffusée, tandis que la clé privée doit rester secrète.

### La signature numérique
On peut se servir de la clé privée pour signer un fichier ou un message afin d'assurer son authenticité.
On crée généralement un "substrat" du contenu à signer, sur lequel on applique le chiffrement, plutôt que de chiffrer l'intégralité de ce contenu, ce qui est plus rapide.
Il est ensuite possible, en utilisant la clé publique, de vérifier que le fichier et authentique, c'est à dire qu'il n'a pas été modifié et qu'il a bien été signé avec la clé privée correspondante.

### Le chiffrement
On peut également chiffrer des fichiers ou des messages en utilisant la clé publique. On s'assure ainsi que seul le détenteur de la clé privée peut les déchiffrer, et donc les lire.

## Clé maitresse et sous-clés

Les sous-clés sont similaires à des clés simples, excepté qu'elles sont automatiquement associées à une paire de clés maitresse. Une sous-clé de chiffrement est créée automatiquement en même temps que la clé maitresse. Vous pouvez créer une autre sous-clé dédiée aux signatures.

Si n'importe qui d'autre accède à votre clé privée maitresse ou ses sous-clés privées, elles lui permettront de s'approprier votre identité, et de faire tout ce que vous pouviez faire avec. Gardez donc toutes vos clés privées en sureté. Il est conseillé de faire une sauvegarde de sa clé principale au cas où elle serait perdue.

Votre clé privée maitresse devrait être gardée très, très en sureté (sur un support externe, éventuellement chiffré, sous clé, surveillé par un garde cyborg-ninja, dans un abri antiatomique). Cependant, appliquer cette politique pour toutes vos clés ne serait pas pratique.

Votre clé privée maitresse servira donc uniquement à :
* la signature de clés d'autres personnes
* la génération de nouvelles sous-clés
* la révocation de sous-clés

Les sous-clés pourront être conservées sur votre ordinateur principal, et les parties publiques de ces paires seront à envoyer à un serveur de clés publique. Su vos sous-clés sont compromises mais que votre clé maitresse est toujours en sécurité, vous pouvez ainsi révoquer vos sous-clés. Vous pouvez ensuite les remplacer par de nouvelles sous-clés sans devoir rebâtir votre réputation ni impacter celle d'autres gens dont vous avez signé les clés avec la vôtre.

# La pratique

## Configuration de GnuPG

On va commencer par indiquer quels algorithmes cryptographiques utiliser et dans quel ordre de préférence dans le fichier de configuration de gnupg. Lancez la commande `gpg --list-keys` (qui sert à lister vos clés). Si c'est la première fois que vous lancez gnupg, cela aura pour effet de créer le fichier de configuration et d'autres fichiers utiles avec des valeurs par défaut. Ouvrez ensuite le fichier  `~/.gnupg/gpg.conf` avec votre éditeur de texte préféré et ajoutez-y les lignes qui suivent (pour rappel, les lignes commençant pas `#` sont des commentaires).
```
# Crypto preferences

# For the keys we create
cert-digest-algo SHA256

# For the signatures and other things we generate
default-preference-list SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES ZLIB BZIP2 ZIP Uncompressed
```

Si vous utilisez `caff`, ajoutez également les mêmes lignes à `~/.caff/gnupghome/gpg.conf`.

## Génération de la paire de clé principale

Une fois cela fait, lancez la commande `gpg --gen-key` et répondez au questions qui vous seront posées de la manière suivante :
* Générer une paire de clés en utilisant RSA et RSA (pour la sous-clé de chiffrement) : `1`
* Utiliser une clé de 4096 bits : `4096`
* Choisir une date d'expiration (vous devrez penser à prolonger la validité de cette clé ou en changer avant cette date, ne l'oubliez pas !), par exemple 2 ans : `2y`
* Valider le choix si la date indiquée est bonne : `y`
* Real name: votre nom complet (`Clark Kent`)
* Email address: votre adresse électronique (`clark.kent@dayliplanet.com`)
* Comment: rien
* Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? : `o`
* Choisissez une phrase de passe que vous êtes [sûr de retenir](http://www.xkcd.com/936/) et [que vous n'utilisez pas pour autre chose](http://www.xkcd.com/792/) (ex : "Dans 2 ans, je n'utiliserai plus cette phrase de passe." ou "F**k la norme & le C ; vive PHP3 !!!").
* Saisissez de nouveau votre phrase de passe.

Si le gnupg vous dit qu'il n'y a pas assez d'entropie, lancez des logiciels bien lourd comme blender ou emacs et faites des trucs dedans, bougez la souris, etc. jusqu'à ce que ça soit bon.

Les dernières lignes affichées indiquent le nom court des clés que vous venez de créer.

### Ajout d'identités (nom et/ou adresses électronique différents)

Vous pouvez utiliser la même clé pour signer avec plusieurs identités, par exemple si vous avez plusieurs adresses électroniques. À noter que la clé publique étant la même, on peut voir qu'elles sont liées.

* `gpg --edit-key abcd1337`
* `adduid`
* Real name: `Superman`
* Email address: `superman@hero-corp.biz`
* Comment:
* Validez et saisissez la même phrase de passe
* enregistrez en tapant `save`

Vous pouvez ajouter une photo avec `addphoto` (toujours après avoir utilisé `gpg --edit-key`) mais cela n'a rien d'obligatoire (`man gpg`).

#### Choix de l'identité principale

Nécessaire seulement si vous avez ajouté des identités et que vous ne voulez pas laisser la première comme principale.

* `gpg --edit-key 6AA15948`
* `uid 1` (où 1 est le numéro de l'identité choisie, à côté de laquelle s'affichera juste après une étoile pour dire qu'elle est sélectionnée)
* `primary`
* `save`

## Génération du certificat de révocation de la clé principale

En prévision de la perte ou du vol de votre clé principale, nous allons générer immédiatement un certificat de révocation pour cette clé, que vous garderez dans un endroit sûr.

* `gpg --armor --output /media/usbkeytwo/revoke-my-rsa-key.gpg --gen-revoke ABCD1337` (où /media/usbkeytwo est un endroit différent de celui où est la clé principale, sinon ça ne sert à rien vu qu'elle permet de générer le certificat de révocation).
* Répondez au questions qui vous seront posées de la manière suivante :
* Générer un certificat de révocation : oui `y`
* Raison : aucune `0`
* Description : rien
* Valider le choix si la date indiquée est bonne : `y`
* Saisissez votre phrase de passe quand elle est demandée

## Ajout d'une sous-clé de signature

* Trouvez l'ID de votre clé : `gpg --list-keys "Clark Kent"`
* `gpg --edit-key ABCD1337`
* `addkey`
* Saisissez votre phrase de passe.
* Choisissez l'option "RSA (sign only)" : `4`
* Choisissez une taille de clé de 4096 bits : `4096`
* Définissez une date d'expiration : `2y`
* Validez vos choix `o`.
* Faites des choses, ouvrez des programmes, etc. jusqu'à ce qu'il y ai assez d'entropie pour générer des valeurs aléatoires afin de créer les clés.
* Sauvegardez la clé : `save`

## Supprimer la clé privée principale de son ordinateur

* `cp -r ~/.gnupg /media/usbkeyone`.
* Trouvez les IDs de vos sous-clé privée : `gpg --list-secret-keys "Clark Kent"`
* Exportez les sous-clés :
 * `gpg --export-secret-subkeys 08366565! CACAD34D! > subkeys`
 * Note : N'oubliez pas les points d'exclamation !
* Exportez également votre clé publique maitresse : `gpg --export ABCD1337 > pubkeys`
* Supprimez votre clé maitresse : `gpg --delete-secret-key ABCD1337`
* Réimportez vos clés : `gpg --import pubkeys subkeys`
* Vérifiez que `gpg -K` vous affiche sec# et non un simple sec pour votre clé privée. Cela signifie que la clé secrète n'est pas réellement présente. 

## Envoie de clés sur un serveur de clés
`gpg --keyserver subkeys.pgp.net --send-key ABCD1337` (vous pouvez évidemment remplacer subkeys.pgp.net par l'adresse d'un autre serveur)

# Signature de clés

## Réseau de confiance

Vous avez maintenant vos clés ! Oui, mais voilà, qui me dit que c'est bien votre identité qui est indiqué par la clé ? Pour s'assurer de cela, on se repose sur un réseau de confiance. Je vous connais ou j'ai un moyen de vérifier qui vous êtes (avec une pièce d'identité par exemple), je vais donc signer votre clé avec la mienne pour confirmer que vous êtes bien qui vous prétendez. Les gens qui me font confiance (directement ou parce qu'ils font confiance à des gens qui eux-même me font confiance) auront donc tendance à penser que vous êtes bien qui vous prétendez. Plus il y aura de personnes faisant partie de leur cercle de confiance qui confirmeront votre identité et plus ils auront tendance à penser que vous êtes bien la personne identifiée par votre clé.

## Signing party

Un moyen simple de faire grandir ce réseau de confiance est d'organiser des [key signing parties](http://fr.wikipedia.org/wiki/Key_signing_party). Chacun vient avec l'emprunte de sa clé sur un petit bout de papier (ou sur un support numérique, mais c'est moins pratique) et une preuve de son identité. On vérifie la preuve de l'identité de la personne en question, on prend son petit papier, et un peu plus tard on signe les clés des gens que l'on a rencontré.

Il est préférable d'avoir envoyé votre clé publique sur un serveur suffisamment de temps auparavant pour que les autres participants puissent la récupérer par ce biais.

Évidemment, il n'est pas interdit d'en profiter pour discuter, boire un verre, etc.

# Et après??

Voilà, vous avez vos clés, elles sont signés par d'autres personnes… et vous voudriez bien vous en servir pour des choses utiles. Voilà donc quelques idées :
* Vous pouvez signez ou chiffrez vos mails.
 * Si vous utilisez un client lourd, regardez si c'est géré par défaut, sinon il existe probablement des plugins comme [enigmail](https://www.enigmail.net/).
 * Si vous utilisez un webmail, il est peut-être quand même possible d'utiliser une extension pour votre navigateur qui permet de faire le minimum, par exemple [mailvelope](http://www.mailvelope.com/).
* Vous pouvez les utilisez pour signer les paquetages que vous faites pour une distribution comme Debian, ou des programmes/fichiers que vous distribuez.
* Vous pouvez les utilisez pour chiffrer des données sensibles (vous savez, ces vidéos très… spéciales que vous avez tourné cet été :oÞ).

# Liens

* http://www.bortzmeyer.org/nouvelle-cle-pgp.html
* http://keyring.debian.org/creating-key.html
* https://wiki.debian.org/fr/Subkeys
* https://alexcabal.com/creating-the-perfect-gpg-keypair/
* https://github.com/flamsmark/documentation/blob/master/gpg/smartcard-keygen.md
