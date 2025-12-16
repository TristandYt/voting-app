
---

```markdown
# Application de Vote Distribuée Asynchrone

Ce projet présente une application de vote complète construite selon une architecture de **microservices**, conçue pour être performante et tolérante aux pannes grâce à un système de messagerie asynchrone. L'intégralité de l'application est conteneurisée à l'aide de Docker pour garantir un environnement de déploiement fiable et cohérent.

## 🏛️ Vue d'Ensemble de l'Architecture

L'architecture est composée de cinq services orchestrés par Docker. 

| Service | Technologie | Rôle Architecturel |
| :--- | :--- | :--- |
| **`vote`** | Python/Flask | **Front-end utilisateur.** Enregistre l'intention de vote et la place immédiatement dans la queue. |
| **`redis`** | Cache (Queue) | **Système de Messagerie (Tampon).** Stockage temporaire des votes, désynchronisant le front-end de la base de données. |
| **`worker`** | .NET Core | **Traitement Asynchrone.** Extrait les votes de la queue et les persiste dans PostgreSQL. |
| **`db`** | PostgreSQL | **Base de Données Persistante.** Stockage fiable des résultats. |
| **`result`** | Node.js | **Front-end en Temps Réel.** Lit les totaux de la BDD et met à jour l'affichage dynamiquement. |

Le flux de données est strictement asynchrone pour la tolérance aux pannes : **Vote** → **Redis** → **Worker** → **PostgreSQL** → **Result**.

---

## 🚀 Démarrage de l'Environnement (Développement Local)

Cette méthode utilise le fichier `docker-compose.yaml` et est recommandée pour le développement et les tests unitaires sur une seule machine.

### 1. Lancement de la Stack

Exécutez cette commande depuis la racine du projet :

```bash
docker compose up -d

```

###2. Accès aux InterfacesUne fois tous les services démarrés (vérifiez le statut des conteneurs), accédez aux applications via les ports mappés :

| Interface | Adresse d'Accès |
| --- | --- |
| **Interface de Vote** | `http://localhost:5000` |
| **Interface des Résultats** | `http://localhost:5001` |

###3. Nettoyage de l'Environnement LocalPour arrêter et supprimer tous les conteneurs, les réseaux, les volumes de données (`postgres-data`), et les images construites :

```bash
docker compose down --volumes --rmi all

```

---

##☁️ Déploiement en Production (Docker Swarm)Pour migrer vers un environnement de production multi-nœuds, nous utilisons Docker Swarm pour l'orchestration, la redondance et la répartition de charge.

###1. Préparation des Images (Balises)`docker stack deploy` ne construit pas d'images. Les images doivent être construites et balisées localement (sur le Manager) ou disponibles sur un Registry externe.

```bash
# Construit les images nécessaires (ex: voting-app_vote, voting-app_worker, etc.)
docker compose build

```

###2. Initialisation du SwarmInitialisez le mode Swarm sur la machine Manager :

```bash
docker swarm init

```

> **Note :** Si la machine est déjà dans un Swarm, exécutez d'abord `docker swarm leave --force`.

###3. Déploiement de la StackDéployez l'application en utilisant le fichier `docker-stack.yaml` qui inclut les règles de haute disponibilité (`replicas` et `placement`) :

```bash
docker stack deploy -c docker-stack.yaml vote-app

```

###4. Vérification et Accès* **Vérifier l'état des Services :**
```bash
docker stack services vote-app

```


*(Confirmez que les services avec réplication, comme `vote` et `result`, affichent le nombre attendu de réplicas.)*
* **Accès :** L'accès se fait via les ports `5000` et `5001` de n'importe quelle adresse IP de nœud participant au Swarm.

###5. Maintenance et Nettoyage du SwarmPour supprimer la stack déployée :

```bash
docker stack rm vote-app

```

Pour désactiver complètement le mode Swarm sur votre machine (Manager) :

```bash
docker swarm leave --force

```

---

##🛠️ Dépannage et Administration###Consultation des LogsPour diagnostiquer des problèmes sur un service spécifique dans Swarm :

```bash
docker service logs vote-app_<nom_du_service>
# Exemple : docker service logs vote-app_worker

```

###Configuration des Variables d'EnvironnementToutes les configurations (mots de passe, noms d'hôtes de services) sont gérées par des variables d'environnement dans les fichiers Compose/Stack. Les services utilisent les noms de service Docker (`redis`, `db`) pour la communication inter-conteneurs.

---

##💡 Contribution et LicenceCe projet est sous licence MIT. Nous encourageons les contributions (via *pull requests*) et les retours (via *issues*) pour améliorer la robustesse et l'architecture de cette application.

```

```