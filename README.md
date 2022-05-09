# Docker-Learning-Area

## ./docker-docs-guides

[https://docs.docker.com/]

### Get Started

[https://docs.docker.com/get-started/]

#### Partie 1 : Premier pas

Lancez le tutoriel :

    docker run -d -p 80:80 docker/getting-started

-d : exécuter le conteneur en mode détaché (en arrière-plan)
-p : 80:80- mapper le port 80 de l'hôte sur le port 80 du conteneur
docker/getting-started : l'image à utiliser

#### Partie 2 : Exemple d'application

##### Créer l'image du conteneur de l'application

Télécharger l'application :
[https://github.com/docker/getting-started/tree/master/app]

0 - cd ./docker-docs-guides/getting-started-example/app

1 - Créez un fichier nommé Dockerfile dans le même dossier que le fichier package.jsonavec le contenu suivant.

    # syntax=docker/dockerfile:1
    FROM node:12-alpine
    RUN apk add --no-cache python2 g++ make
    WORKDIR /app
    COPY . .
    RUN yarn install --production
    CMD ["node", "src/index.js"]
    EXPOSE 3000

2 - Si vous ne l'avez pas déjà fait, ouvrez un terminal et allez dans le apprépertoire avec le Dockerfile. Créez maintenant l'image du conteneur à l'aide de la docker buildcommande.

    docker build -t my-first-image .

##### Démarrer un conteneur d'applications

1 - Démarrez votre conteneur à l'aide de la docker runcommande et spécifiez le nom de l'image que nous venons de créer :

    docker run -dp 3000:3000 my-first-image

2 - Après quelques secondes, ouvrez votre navigateur Web sur [http://localhost:3000]. Vous devriez voir notre application.

#### Partie 3 : Mettre à jour l'application

##### Mettre à jour le code source

1 - Dans le src/static/js/app.jsfichier, mettez à jour la ligne 56 pour utiliser le nouveau texte vide.

    - <p className="text-center">No items yet! Add one above!</p>
    + <p className="text-center">You have no todo items yet! Add one above!</p>

2 - Construisons notre version mise à jour de l'image, en utilisant la même commande que nous avons utilisée auparavant.

    docker build -t my-second-image .

3 - Commençons un nouveau conteneur en utilisant le code mis à jour.

    docker run -dp 3000:3000 my-second-image

==> ERROR

##### Remplacer l'ancien contenant

1 - Obtenez l'ID du conteneur à l'aide de la docker pscommande.

    docker ps

2 - Utilisez la docker stop commande pour arrêter le conteneur.

    docker stop <the-container-id>

3 - Une fois le conteneur arrêté, vous pouvez le supprimer à l'aide de la docker rmcommande.

    docker rm <the-container-id>

Arrêter et supprimer un conteneur en une seule commande :

    docker rm -f <the-container-id>

##### Démarrez le conteneur d'applications mis à jour

1 - Maintenant, démarrez votre application mise à jour.

    docker run -dp 3000:3000 getting-started

2 - Actualisez votre navigateur sur [http://localhost:3000] et vous devriez voir votre texte d'aide mis à jour !

#### Partie 4 : Partager l'application

Créer un dépot sur [https://hub.docker.com/repository/docker/970m/getting-started]

##### Pousser l'image

1 - Dans la ligne de commande, essayez d'exécuter la commande push que vous voyez sur Docker Hub. Notez que votre commande utilisera votre espace de noms, pas "docker".

    docker push docker/my-second-image
    
    The push refers to repository [docker.io/docker/my-second-image]
    An image does not exist locally with the tag: docker/my-second-image

    docker image ls

Pourquoi a-t-il échoué ? La commande push recherchait une image nommée docker/my-second-image, mais n'en a pas trouvé. Si vous exécutez docker image ls, vous n'en verrez pas non plus.

Pour résoudre ce problème, nous devons "tagger" notre image existante que nous avons construite pour lui donner un autre nom.

2 - Connectez-vous au Docker Hub à l'aide de la commande :

    docker login -u YOUR-USER-NAME.

Utilisez la docker tag commande pour donner my-second-image d'un nouveau nom à l'image. Assurez-vous d'échanger YOUR-USER-NAME avec votre ID Docker.

    docker tag my-second-image YOUR-USER-NAME/my-second-image

4 - Maintenant, essayez à nouveau votre commande push. Si vous copiez la valeur de Docker Hub, vous pouvez supprimer la tagnamepartie, car nous n'avons pas ajouté de balise au nom de l'image. Si vous ne spécifiez pas de balise, Docker utilisera une balise appelée latest.

    docker push YOUR-USER-NAME/my-second-image

##### Exécutez l'image sur une nouvelle instance

1 - Ouvrez votre navigateur pour jouer avec Docker

[https://labs.play-with-docker.com/]

2 - Cliquez sur Connexion , puis sélectionnez docker dans la liste déroulante.

3 - Connectez-vous avec votre compte Docker Hub.

4 - Une fois connecté, cliquez sur l' option AJOUTER UNE NOUVELLE INSTANCE dans la barre de gauche.

5 - Dans le terminal, démarrez votre application fraîchement poussée.

    docker run -dp 3000:3000 YOUR-USER-NAME/my-seconde-image

Vous devriez voir l'image se dérouler et finalement démarrer !

6 - Cliquez sur le badge 3000 lorsqu'il apparaît et vous devriez voir l'application avec vos modifications ! Hourra ! Si le badge 3000 n'apparaît pas, vous pouvez cliquer sur le bouton "Ouvrir le port" et saisir 3000.

#### Partie 5 : Rendre la base de données persistante

##### Le système de fichiers du conteneur

1 - Démarrez un ubuntuconteneur qui créera un fichier nommé /data.txt avec un nombre aléatoire compris entre 1 et 10000.

    docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"

2 - Validez que nous pouvons voir la sortie execen entrant dans le conteneur. Vous pouvez utiliser la docker execcommande pour faire de même. Vous devez obtenir l'ID du conteneur (utilisez docker ps pour l'obtenir) et obtenir le contenu avec la commande suivante.

    docker exec <container-id> cat /data.txt

3 - Maintenant, commençons un autre ubuntuconteneur (la même image) et nous verrons que nous n'avons pas le même fichier.

    docker run -it ubuntu ls /

Et regarde! Il n'y a pas de fichier data.txt ici ! C'est parce qu'il n'a été écrit dans l'espace de travail que pour le premier conteneur.

    docker exec <container-id> ls

Allez-y et supprimez le premier conteneur à l'aide de la commande docker:

    rm -f <container-id>

##### Rendre persistantes les données todo

En créant un volume et en l'attachant (souvent appelé "montage") au répertoire dans lequel les données sont stockées, nous pouvons conserver les données. Au fur et à mesure que notre conteneur écrit dans le todo.dbfichier, il sera conservé sur l'hôte dans le volume.

Comme mentionné, nous allons utiliser un volume nommé . Considérez un volume nommé comme un simple ensemble de données. Docker conserve l'emplacement physique sur le disque et vous n'avez qu'à vous souvenir du nom du volume. Chaque fois que vous utilisez le volume, Docker s'assurera que les données correctes sont fournies.

1 - Créez un volume à l'aide de la docker volume create commande.

    docker volume create todo-db

2 - Arrêtez et supprimez à nouveau le conteneur de l'application todo dans le tableau de bord (ou avec docker rm -f \<id\>), car il est toujours en cours d'exécution sans utiliser le volume persistant.

    docker rm -f <container-id>

    (docker exec <container-id> ls /etc/todos)

3 - Démarrez le conteneur de l'application todo, mais ajoutez l' -vindicateur pour spécifier un montage de volume. Nous utiliserons le volume nommé et le monterons sur /etc/todos, ce qui capturera tous les fichiers créés sur le chemin.

    docker run -dp 3000:3000 -v todo-db:/etc/todos my-second-image

4 - Une fois le conteneur démarré, ouvrez l'application et ajoutez quelques éléments à votre liste de tâches.

Éléments ajoutés à la liste de tâches

5 - Arrêtez et supprimez le conteneur de l'application todo. Utilisez le tableau de bord ou docker pspour obtenir l'ID, puis docker rm -f \<id\> pour le supprimer.

6 - Démarrez un nouveau conteneur en utilisant la même commande ci-dessus.

7 - Ouvrez l'application. Vous devriez voir vos articles toujours dans votre liste !

8 - Allez-y et retirez le conteneur lorsque vous avez terminé de vérifier votre liste.

##### Plongez dans le volume

De nombreuses personnes demandent fréquemment "Où Docker stocke-t-il réellement mes données lorsque j'utilise un volume nommé?" Si vous voulez savoir, vous pouvez utiliser la docker volume inspect commande.

    docker volume inspect todo-db

#### Partie 6 : Utiliser des supports de liaisons

##### Comparaisons rapides de type de volume

||Volumes nommés|Lier les montures|
|---|--------------|-----------------|
|Emplacement de l'hôte|Docker choisit|Vous contrôlez|
|Exemple de montage (avec -v)|mon-volume:/usr/local/data|/chemin/vers/data:/usr/local/data
|Remplit le nouveau volume avec le contenu du conteneur|Oui|Non|
|Prend en charge les pilotes de volume|Oui|Non|

##### Démarrer un conteneur en mode dev

Pour exécuter notre conteneur afin de prendre en charge un workflow de développement, nous procéderons comme suit :

Montez notre code source dans le conteneur
Installer toutes les dépendances, y compris les dépendances "dev"
Démarrez nodemon pour surveiller les modifications du système de fichiers

1 - Assurez-vous qu'aucun my-second-image conteneur précédent n'est en cours d'exécution.

2 - Exécutez la commande suivante à partir du répertoire de l'application. Nous vous expliquerons ensuite ce qui se passe :

    docker run -dp 3000:3000 \
     -w /app -v "$(pwd):/app" \
     node:12-alpine \
     sh -c "yarn install && yarn run dev"

-dp 3000:3000 : pareil qu'avant. Exécuter en mode détaché (arrière-plan) et créer un mappage de port
-w /app : définit le "répertoire de travail" ou le répertoire actuel à partir duquel la commande sera exécutée
-v "$(pwd):/app" : lier monter le répertoire courant de l'hôte dans le conteneur dans le /apprépertoire
node:12-alpine : l'image à utiliser. Notez qu'il s'agit de l'image de base de notre application à partir du Dockerfile
sh -c "yarn install && yarn run dev" : la commande. Nous démarrons un shell en utilisant sh(alpine n'a pas bash) et en cours d'exécution yarn installpour installer toutes les dépendances, puis en exécutant yarn run dev. Si nous regardons dans le package.json, nous verrons que le devscript démarre nodemon.

3 - Vous pouvez regarder les journaux en utilisant docker logs. Vous saurez que vous êtes prêt à partir lorsque vous verrez ceci :

    docker logs -f <container-id>

4 - Maintenant, modifions l'application. Dans le src/static/js/app.jsfichier, changeons le bouton "Ajouter un élément" pour dire simplement "Ajouter". Ce changement sera sur la ligne 109 :

    - {submitting ? 'Adding...' : 'Add Item'}
    + {submitting ? 'Adding...' : 'Add'}

5 - Actualisez simplement la page (ou ouvrez-la) et vous devriez voir le changement reflété dans le navigateur presque immédiatement. Le redémarrage du serveur Node peut prendre quelques secondes, donc si vous obtenez une erreur, essayez simplement d'actualiser après quelques secondes.

6 - N'hésitez pas à apporter d'autres modifications que vous souhaitez apporter. Lorsque vous avez terminé, arrêtez le conteneur et créez votre nouvelle image en utilisant :

    docker build -t my-seconde-image .
