Voici un fichier complet qui combine tout le README dans un seul fichier :

````markdown
# 🗳️ Application de Vote Distribuée (Docker Swarm Ready)

Ceci est une application de vote simple et distribuée, conteneurisée et orchestrée avec Docker Compose pour le développement et Docker Swarm pour la haute disponibilité.

## 🏛️ Architecture de l'Application

L'application est décomposée en cinq services qui communiquent de manière asynchrone :

| Service   | Technologie    | Rôle                                                                 |
| --------- | -------------- | -------------------------------------------------------------------- |
| **`vote`** | Python/Flask   | Interface utilisateur. Envoie les votes vers Redis.                 |
| **`redis`** | Cache (Queue) | File d'attente pour le stockage temporaire des votes.               |
| **`worker`** | .NET Core     | Traite les messages de Redis et les insère dans PostgreSQL.         |
| **`db`**    | PostgreSQL     | Base de données persistante pour les résultats.                     |
| **`result`** | Node.js       | Interface utilisateur affichant les résultats en temps réel.       |

Le flux de données est : **Vote** → **Redis** → **Worker** → **PostgreSQL** → **Result**.

---

## 🚀 Démarrage en Environnement Local (Docker Compose)

Cette méthode utilise le fichier `docker-compose.yaml` pour le développement et les tests sur une seule machine.

### 1. Lancement de la Stack

Assurez-vous d'être à la racine du projet.

```bash
docker compose up -d
````

### 2. Accéder aux Interfaces Utilisateurs

Une fois que les conteneurs sont lancés, vous pouvez accéder aux différentes interfaces utilisateurs via les ports suivants :

* **Vote Interface** : [http://localhost:5000](http://localhost:5000)
* **Result Interface** : [http://localhost:5001](http://localhost:5001)

Les services seront accessibles en arrière-plan, et les logs peuvent être consultés via Docker :

```bash
docker compose logs -f
```

---

## 🌐 Déploiement avec Docker Swarm

Pour un environnement de production, Docker Swarm peut être utilisé pour orchestrer les services afin d'assurer la haute disponibilité.

### 1. Initialisation de Docker Swarm

Si Docker Swarm n'est pas encore initialisé, vous pouvez le faire avec la commande suivante :

```bash
docker swarm init
```

### 2. Déploiement de la Stack avec Docker Swarm

Pour déployer la stack dans Docker Swarm, utilisez le fichier `docker-stack.yml` :

```bash
docker stack deploy -c docker-stack.yml vote-app
```

### 3. Vérification du Déploiement

Une fois la stack déployée, vous pouvez vérifier l'état des services avec la commande suivante :

```bash
docker stack services vote-app
```

Cela vous montrera les services en cours d'exécution et leurs états.

---

## 🛠️ Configuration et Personnalisation

### Variables d'Environnement

Les services utilisent des variables d'environnement pour la configuration. Vous pouvez les modifier dans le fichier `.env` à la racine du projet :

* **`POSTGRES_PASSWORD`** : Mot de passe pour la base de données PostgreSQL.
* **`REDIS_HOST`** : Hôte du service Redis.
* **`DB_HOST`** : Hôte de la base de données PostgreSQL.
* **`VOTE_INTERFACE_PORT`** : Port pour l'interface de vote.
* **`RESULT_INTERFACE_PORT`** : Port pour l'interface de résultats.

---

## 🔧 Dépannage

### Problèmes Courants

* **Service `redis` ne démarre pas** : Assurez-vous que Docker est correctement configuré et que le port 6379 est libre.
* **Problèmes de connexion à la base de données PostgreSQL** : Vérifiez les variables d'environnement, en particulier `DB_HOST` et `POSTGRES_PASSWORD`.

Pour consulter les logs d'un service spécifique :

```bash
docker logs <nom_du_service>
```

---

## 💡 Aide et Contribution

Si vous avez des suggestions ou souhaitez contribuer à l'amélioration de l'application, n'hésitez pas à soumettre une pull request ou à ouvrir une issue.

---

## 📄 Licence

Ce projet est sous licence MIT. Voir le fichier [LICENSE](LICENSE) pour plus de détails.

```

Ce fichier README est complet et peut être utilisé directement dans le projet. Il couvre les étapes pour démarrer l'application localement avec Docker Compose, ainsi que le déploiement dans un environnement de production avec Docker Swarm. Vous pouvez l'ajuster si nécessaire pour les configurations spécifiques à votre projet.
```
