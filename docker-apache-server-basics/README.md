# DOCKER

## Container management

1. Récupérer un image sur le docker hub

      `docker pull ubuntu/apache2`

2. Vérifier les images disponibles

    `docker image ls`
    `docker images`

3. Lancer un conteneur en mode interactif à partir d'une image

    `docker run -it ubuntu/apache2`

4. Vérifier que le conteneur est bien lancé

    `docker ps`

5. Arreter un conteneur

    `docker stop 1ff4cc35f5c4`

6. Lancer un conteneur en mode détaché (arriere plan)

    `docker run -it -d ubuntu/apache2`

7. Démarrer un conteneur en redirigeant le port 8080 vers le port 80 du conteneur

    `docker run -d -p 8080:80 ubuntu/apache2`

8. Vérifier en se rendant sur la page http://127.0.0.1:8080/

9. Rentrer dans le conteneur pour y effectuer des actions

    `docker exec -ti 1909def92c49 bash`

10. Nettoyer le systeme

    `docker system prune`

## Dockerfile
