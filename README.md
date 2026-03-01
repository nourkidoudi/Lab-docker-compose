# Lab : Déploiement d'une Application Multi-Tiers avec Docker Compose

## Projet : Liste de Contacts

Ce projet implémente une application de gestion de contacts avec l'architecture suivante :
- **Frontend** : React (Vite, TS) servi par Nginx (multi-stage)
- **Backend** : Node.js (Express, TS, Drizzle ORM) (multi-stage)
- **Base de données** : PostgreSQL

### Commandes Utiles

- Démarrer l'environnement complet :
  ```bash
  docker-compose up --build -d
  ```

- Arrêter l'environnement :
  ```bash
  docker-compose down
  ```

- Vérifier l'état des conteneurs :
  ```bash
  docker-compose ps
  ```

- Vérifier les statistiques (CPU/Memory) :
  ```bash
  docker stats
  ```

### Troubleshooting Courant

- **Le backend ne se connecte pas à la BD** : Vérifiez que les variables d'environnement dans le fichier `.env` ou dans `docker-compose.yml` correspondent bien à celles définies dans le service `database`.
- **Le frontend ne contacte pas l'API** : Assurez-vous que l'API est proxifiée correctement via Nginx.

---

## Tests et Validation

### Test 1 : Connectivité Base de Données
- **Commande** : `docker-compose logs backend` ou exécution d'une requête manuelle.
- **Résultat** : Le backend se connecte avec succès et crée les tables au démarrage s'il utilise Drizzle push ou l'initialisation scriptée (`init.sql`).
- *(Ajouter screenshot ici)*

### Test 2 : API Backend
- **Commande** : `curl http://localhost:5000/health`
- **Résultat attendu** : `{"status":"ok"}`
- *(Ajouter screenshot ici)*

### Test 3 : Frontend
- **Action** : Accéder à `http://localhost:8080/`
- **Résultat attendu** : Affichage de la liste des contacts, possibilité d'ajouter/modifier/supprimer.
- *(Ajouter screenshot ici)*

### Test 4 : Isolation Réseau
- **Commande** : `docker exec -it <frontend_container_id> sh` puis `ping database` ou `curl database:5432`
- **Résultat attendu** : Échec (preuve de l'isolation).
- *(Ajouter screenshot ici)*

### Test 5 : Persistance des Données
- **Action** : Ajouter un contact, exécuter `docker-compose down`, puis `docker-compose up -d`. Le contact est toujours présent.
- *(Ajouter screenshot ici)*

### Test 6 : Limites de Ressources
- **Commande** : `docker stats`
- **Résultat** : La RAM du backend ne dépasse pas 256M, la BD 512M et le frontend 128M.
- *(Ajouter screenshot ici)*

### Test 7 : Health Checks
- **Commande** : `docker-compose ps`
- **Résultat** : Les services passent tous en statut `(healthy)`.
- *(Ajouter screenshot ici)*

## Captures d'Écran Obligatoires

> Veuillez insérer vos captures d'écran ici pour la validation du lab.

1. `docker-compose ps` montrant tous les services "Up" et "healthy"
2. `docker network ls` montrant vos deux réseaux
3. `docker volume ls` montrant votre volume de données
4. `docker stats` montrant les limites de ressources appliquées
5. Interface frontend fonctionnelle affichant des données
6. Test d'ajout de données via l'interface
7. Logs démontrant la connexion backend → database
8. Test de l'isolation réseau (échec de connexion frontend → database)
9. Health check status dans docker inspect
10. Comparaison de taille d'image (backend avec/sans multi-stage)
