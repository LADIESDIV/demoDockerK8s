# ChtiteDev: tp docker

## But : push dans votre registry docker l'image de l'application food

L'application "foo" est en python dans le fichier "foo.py" qui tourne sur le port 5000.

1) Créer l'image docker pour que l'application puisse tourner sur le port 8080 sur votre poste.
   --> cf Dockerfile
   --> docker build -t $userDockerhub/demoDocker .
   --> docker run -p5001:5000 $userDockerhub/demoDocker
3) Pusher l'image sur votre registry [dockerhub](https://hub.docker.com/) (créer un compte).
   --> docker push $userDockerhub/demoDocker
