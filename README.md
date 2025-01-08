# Démo docker et kubernetes pour ChtiteDev

## TP Docker

### But : push dans votre registry docker l'image de l'application food

L'application "foo" est en python dans le fichier "foo.py" qui tourne sur le port 5000.

1) Installer [Docker](https://docs.docker.com/engine/install/).

2) Créer l'image docker pour que l'application puisse tourner sur le port 8080 sur votre poste.

   --> cf Dockerfile

   --> docker build -t $userDockerhub/demoDocker .

   --> docker run -p5001:5000 $userDockerhub/demoDocker

3) Pusher l'image sur votre registry [dockerhub](https://hub.docker.com/) (créer un compte).

   --> docker push $userDockerhub/demoDocker

## TP Kubernetes

### But : déployer l'image de l'application foo sur un minikube

Maintenant que l'application foo est dans votre registry, nous allons l'utiliser pour la déployer dans un minikube.

1) Installer [minikube](https://kubernetes.io/fr/docs/tasks/tools/install-minikube).

2) Créer le manifest en yaml ou json pour déployer l'application (un kind de type deployment).

   --> fichier "deploy.yaml" dans le repo.

   --> ```kubectl apply -f deploy.yaml```

3) Créer le service pour contacter l'application (en clusterIP pour une communication en interne de minikube).

   --> fichier "svcClusterIP.yaml" dans le repo.

   --> ```kubectl apply -f svcClusterIP.yaml```

4) Tester que vous arrivez à joindre l'application depuis un pod de test (vous pouvez utiliser l'image "alpine/curl" pour cela, avec l'url à tester en commande d'exec "kubectl run xx -- $URL". L'URL est l'IP du service ou le nom du service).

   --> ```kubectl run curl --restart=Never --rm -it --image alpine/curl -- chtitedev:8080/foo```

5) Ajouter un loadbalancer (exemple avec metallb : ```minikube addons configure metallb```, IP start : 192.168.49.10 et IP end: 192.168.49.250, puis ```minikube addons enable metallb```) à minikube pour créer un service de type LoadBalancer pour contacter l'application depuis l'extérieur de minikube (ie faites un curl pour contacter l'application).

   --> fichier "svcLB.yaml" dans le repo.

   --> ```kubectl apply -f svcLB.yaml```

   --> ```kubectl get svc chtitedevlb``` pour avoir l'ip external puis ```curl $IP:8080/foo```

       ou

   --> ```curl $(kubectl get svc chtitedevlb -o json | jq -r .status.loadBalancer.ingress.[0].ip):8080/foo```

6) Ajouter un ingress (```minikube addons enable ingress``` puis ```kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=90s``` pour attendre que l'ingress s'installe) et rendez l'application joignable via l'ingress (avoir un service de type NodePort puis utiliser le template dans le fichier "ingress.yaml" du repo, FIXME= ip de minikube que nous avons avec la commande ```minikube ip```).

   --> fichier "svcNodeport.yaml" et "ingress.yaml" dans le repo.

   --> ```kubectl apply -f ingress.yaml``` et ```kubectl apply -f svcNodeport.yaml``` 

   --> ```curl $(minikube ip)```
