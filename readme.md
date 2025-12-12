Présentation générale
Ce projet implémente une application de vote distribuée composée de plusieurs services indépendants :
Interface de vote (Python Flask)
Service de résultats (Node.js)
Worker de traitement (C# / .NET 7)
Base de données PostgreSQL
Cache / file de messages Redis
L’architecture repose sur plusieurs conteneurs Docker et peut être déployée en environnement multi-nœuds via Docker Swarn

Lancement en local avec Docker Compose
Construction et lancement :

docker-compose up --build

Arrêt des services :
docker-compose down

Accès aux interfaces :
Vote : http://localhost:8080
Résultats : http://localhost:8888

Mise en place d’un cluster Docker Swarm
Cette section décrit la procédure standard pour créer un cluster composé de :
1 nœud manager
2 nœuds workers

Installation de Docker sur chaque machine
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

Initialiser le Swarm sur le nœud manager
docker swarm init --advertise-addr <IP_MANAGER>         <IP_MANAGER> = ip sur laquelle on utilise docker 

Conserver la commande join affichée par Docker.
Ajouter les deux nœuds workers
Sur chaque machine worker :
docker swarm join --token <TOKEN> <IP_MANAGER>:2377

Vérification sur le manager :
docker node ls

Déploiement de la stack Docker Swarm
Depuis le nœud manager :
docker stack deploy -c docker-stack.yml voting

Commandes de supervision
Lister les services :
docker stack services voting

Lister les conteneurs déployés :
docker stack ps voting

Afficher les logs d’un service :
docker service logs -f voting_vote

Mettre à jour une stack après modification de docker-stack.yml :
docker stack deploy -c docker-stack.yml voting

Supprimer la stack :
docker stack rm voting