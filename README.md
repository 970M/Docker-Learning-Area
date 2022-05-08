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
