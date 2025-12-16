---

# Application de Vote Distribuée Asynchrone

Ce projet présente une **application de vote** distribuée. L'objectif est de garantir une **scalabilité**, une **résilience** face aux pannes, et une **performance** optimale grâce à l'utilisation de Docker pour la conteneurisation.

L'application repose sur un traitement asynchrone des données, où chaque composant est indépendant et communique via une **file de messages** (Redis). Cette approche permet de découpler les différents services et d'assurer une résilience accrue face aux pannes.

L’application est **conteneurisée avec Docker**, garantissant un environnement fiable entre le développement et la production. L'orchestration est assurée par **Docker Compose** pour le développement local et **Docker Swarm** pour un déploiement en production avec haute disponibilité.

---

## 🏛️ Vue d'Ensemble de l'Architecture

L'architecture de l'application repose sur cinq services interconnectés. Voici un aperçu de chaque service et de son rôle :

* **`vote`** (Python / Flask) : Ce service constitue l'interface utilisateur front-end, où les utilisateurs peuvent soumettre leurs votes. Dès qu'un vote est émis, il est envoyé vers Redis pour être traité par les autres services.

* **`redis`** (Redis) : Redis sert de **système de messagerie** (queue) et tamponne les votes de manière asynchrone. Il permet de gérer les pics de charge et de garantir une communication fiable entre les services sans affecter la performance du front-end.

* **`worker`** (.NET Core) : Ce service consomme les messages stockés dans Redis et les traite en arrière-plan. Il persiste les votes dans la base de données PostgreSQL de manière asynchrone.

* **`db`** (PostgreSQL) : La base de données relationnelle qui stocke de manière fiable les résultats des votes. PostgreSQL assure la persistance des données et leur intégrité.

* **`result`** (Node.js) : Ce service affiche les résultats en temps réel. Il lit les données de la base PostgreSQL et met à jour l’interface utilisateur de manière dynamique.

### Flux de données

Le flux de données entre les services suit un modèle **asynchrone**, garantissant ainsi une tolérance accrue aux pannes et une meilleure gestion de la charge :

**Vote → Redis → Worker → PostgreSQL → Result**

---

## 🚀 Démarrage de l’Environnement (Développement Local)

Cette méthode repose sur `docker-compose.yaml` et est recommandée pour le développement et les tests locaux.

### 1. Lancement de la Stack

Depuis la racine du projet, lancez les services définis dans le fichier `docker-compose.yaml` en mode détaché (en arrière-plan) :

```bash
docker compose up -d
```

### 2. Accès aux Interfaces Utilisateurs

Une fois les services lancés, vous pouvez accéder aux interfaces via les URLs suivantes :

| Interface                   | Adresse d’accès                                |
| --------------------------- | ---------------------------------------------- |
| **Interface de Vote**       | [http://localhost:5000](http://localhost:5000) |
| **Interface des Résultats** | [http://localhost:5001](http://localhost:5001) |

### 3. Arrêt et Nettoyage de l’Environnement Local

Pour arrêter et supprimer les conteneurs, réseaux et volumes créés par Docker Compose, exécutez la commande suivante :

```bash
docker compose down --volumes --rmi all
```

Cette commande :

* **Arrête** et **supprime** tous les conteneurs et services.
* Supprime également les **volumes** associés (comme `postgres-data`).
* Supprime les **images** Docker construites lors du lancement.

---

## ☁️ Déploiement en Production (Docker Swarm)

Pour migrer vers un environnement de production avec haute disponibilité, nous utilisons **Docker Swarm** pour l’orchestration et la gestion des services.

### 1. Préparation des Images Docker

Avant de déployer la stack sur Docker Swarm, vous devez d’abord construire les images Docker nécessaires. Exécutez la commande suivante pour construire toutes les images à partir du fichier `docker-compose.yml` :

```bash
docker compose build
```

### 2. Initialisation du Mode Swarm

Sur le nœud Manager, initialisez Docker Swarm avec la commande suivante :

```bash
docker swarm init
```

> **Note :**
> Si la machine fait déjà partie d'un Swarm, vous pouvez la quitter avec la commande suivante :
>
> ```bash
> docker swarm leave --force
> ```

### 3. Déploiement de la Stack avec Docker Swarm

Une fois que Swarm est initialisé, déployez la stack à l'aide du fichier `docker-stack.yml` en exécutant :

```bash
docker stack deploy -c docker-stack.yml vote-app
```

### 4. Vérification du Déploiement

Pour vérifier l’état des services déployés dans Docker Swarm, utilisez la commande suivante :

```bash
docker stack services vote-app
```

Cela vous permettra de confirmer que tous les services sont bien en cours d'exécution avec les réplicas attendus.

#### Accès aux Interfaces

Les interfaces **Vote** et **Résultats** seront accessibles via les ports **5000** et **5001** de n’importe quel nœud dans le Swarm.

---

### 5. Maintenance et Nettoyage du Swarm

#### Suppression de la Stack

Pour supprimer la stack déployée et arrêter tous les services associés, utilisez la commande suivante :

```bash
docker stack rm vote-app
```

#### Quitter le Mode Swarm

Pour désactiver Docker Swarm sur votre nœud Manager, exécutez :

```bash
docker swarm leave --force
```

---

## 🛠️ Dépannage et Administration

### Consultation des Logs (Swarm)

Pour consulter les logs d’un service spécifique dans Swarm, utilisez la commande suivante :

```bash
docker service logs vote-app_<nom_du_service>
```

**Exemple :**

```bash
docker service logs vote-app_worker
```

Cela vous permet de diagnostiquer les problèmes pour un service particulier en consultant ses logs.

### Configuration des Variables d’Environnement

Toutes les configurations (telles que les mots de passe, les hôtes et les ports) sont gérées par des **variables d’environnement** dans les fichiers Compose et Stack.

Les services interagissent via des **noms de services Docker** (par exemple `redis`, `db`), ce qui garantit une résolution DNS automatique entre les conteneurs.

---

## 💡 Contribution et Licence

Ce projet est distribué sous licence **MIT**.
Les contributions sont les bienvenues via **pull requests**, ainsi que les retours et suggestions via **issues**.

---

### Récapitulatif des Commandes

Voici un récapitulatif de toutes les commandes essentielles pour travailler avec cette application :

#### Docker Compose (Développement Local) :

* **Lancer les services** :

  ```bash
  docker compose up -d
  ```
* **Accéder aux services** :

  * Interface de Vote : [http://localhost:5000](http://localhost:5000)
  * Interface des Résultats : [http://localhost:5001](http://localhost:5001)
* **Arrêter et nettoyer l’environnement** :

  ```bash
  docker compose down --volumes --rmi all
  ```

#### Docker Swarm (Production) :

* **Construire les images Docker** :

  ```bash
  docker compose build
  ```
* **Initialiser Swarm** :

  ```bash
  docker swarm init
  ```
* **Déployer la stack** :

  ```bash
  docker stack deploy -c docker-stack.yml vote-app
  ```
* **Vérifier l’état des services** :

  ```bash
  docker stack services vote-app
  ```
* **Supprimer la stack** :

  ```bash
  docker stack rm vote-app
  ```
* **Quitter Swarm** :

  ```bash
  docker swarm leave --force
  ```

#### Dépannage :

* **Consulter les logs d’un service dans Swarm** :

  ```bash
  docker service logs vote-app_<nom_du_service>
  ```

---