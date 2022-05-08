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

##### Créez l'image du conteneur de l'application

Télécharger l'application :
https://github.com/docker/getting-started/tree/master/app

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


