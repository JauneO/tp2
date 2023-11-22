# Proposition d'Amélioration du Système de Gestion de Livres

## Contexte
Actuellement, le système de gestion de livres permet aux utilisateurs de rechercher des livres, mais ne permet pas aux bibliothécaires d'ajouter des livres au système de manière efficace. La demande principale est de faciliter l'ajout de livres par les bibliothécaires, en prenant en compte les particularités liées aux livres usagés et à l'absence d'ISBN pour les livres publiés avant 1972.

## Solution Proposée

### Modification du Processus d'Ajout de Livres

#### 1. Ajout d'un Livre avec ISBN

- **Étape 1**: L'utilisateur entre l'ISBN du livre.
- **Étape 2**: Le système vérifie la validité de l'ISBN.
- **Étape 3**: Le système vérifie si l'ISBN existe déjà dans la base de données.
  - Si l'ISBN existe, le système refuse l'ajout.
  - Si l'ISBN n'existe pas, l'utilisateur peut passer à l'ajout des détails du livre.

#### 2. Ajout d'un Livre sans ISBN (pour les livres publiés avant 1972)

- **Étape 1**: L'utilisateur sélectionne l'option d'ajout sans ISBN.
- **Étape 2**: L'utilisateur entre le titre du livre (qui servira d'identifiant unique).
- **Étape 3**: Le système vérifie si le titre existe déjà dans la base de données.
  - Si le titre existe, le système refuse l'ajout.
  - Si le titre n'existe pas, l'utilisateur peut ajouter les détails du livre.

### Diagramme de Cas d'Utilisation

```mermaid
graph TD
  A[Bibliothécaire] -->|Utilise| B[Gestion de Livres]
  B -->|Inclut| C[Entrer ISBN/Titre]
  B -->|Inclut| D[Validation ISBN]
  B -->|Inclut| E[Vérification Existence Livre]
  B -->|Inclut| F[Ajout Détails Livre]
```

###  Diagramme de Séquence (Ajout de Livre)

```mermaid
sequenceDiagram
  participant Bibliothécaire as Utilisateur
  participant Frontend as Frontend (Vue.js)
  participant Backend as Backend (Express.js)
  participant Database as MongoDB (Base de données)

  Bibliothécaire->>Frontend: Entrez ISBN
  Frontend->>Backend: Requête d'ajout avec ISBN
  Backend->>Database: Vérification ISBN
  Database-->>Backend: ISBN valide?
  Backend-->>Frontend: Réponse

  alt ISBN valide
    Frontend->>Backend: Requête de vérification existence
    Backend->>Database: Recherche ISBN
    Database-->>Backend: Livre existant?
    Backend-->>Frontend: Réponse
    alt Livre inexistant
        Frontend->>Backend: Ajout des détails du livre
        Backend->>Database: Enregistrement du livre
        Database-->>Backend: Livre enregistré
        Backend-->>Frontend: Réponse
    end
  else ISBN non valide
    Backend-->>Bibliothécaire: ISBN non valide
    alt Sélectionnez Ajout sans ISBN
        Bibliothécaire->>Frontend: Sélectionnez Ajout sans ISBN
        Frontend->>Backend: Requête d'ajout sans ISBN
        Backend->>Database: Vérification Titre
        Database-->>Backend: Titre existant?
        Backend-->>Frontend: Réponse
        alt Titre non existant
            Bibliothécaire->>Frontend: Saisie des détails du livre
            Frontend->>Backend: Ajout des détails du livre
            Backend->>Database: Enregistrement du livre
            Database-->>Backend: Livre enregistré
            Backend-->>Frontend: Réponse
        else Titre existant
            Backend-->>Bibliothécaire: Titre existant, ajout impossible
        end
    end
  end
```
