# 🚀 Portail d'Avis Médias - Architecture Découplée (Headless)

Ce projet est une application web moderne et performante conçue pour mon portfolio professionnel. Il démontre une maîtrise complète des architectures web découplées, combinant la robustesse de modélisation d'un CMS d'entreprise et la réactivité d'un framework frontend de pointe.

## 🛠️ Stack Technique & Architecture

*   **Backend (API RESTful)** : Drupal 10 (PHP 8.2 & Apache) orchestré de manière isolée via **Docker & Docker Compose** pour garantir la portabilité parfaite de l'environnement de développement.
*   **Frontend (Single Page Application)** : Vue.js 3 (`Composition API` avec syntaxe `<script setup>`) propulsé par **Vite** et stylisé avec **Tailwind CSS v4** pour des performances de build et de rendu instantanées.
*   **Modélisation & Flux** : Modélisation de types de contenus complexes liés à des dictionnaires de taxonomies dynamiques. Exposition des données via des endpoints JSON configurés sur-mesure via le moteur de requêtes **Drupal Views**.
*   **Sécurité & Réseau** : Intégration d'un **proxy de développement inverse sur Vite** (`server.proxy`) permettant une communication fluide de serveur à serveur, éliminant les problématiques de CORS tout en simulant un environnement de production.

## ⚙️ Installation et Lancement local

### 1. Prérequis
*   [Docker Desktop](https://docker.com) installé et actif.
*   [Node.js](https://nodejs.org) (v24+) et npm (v11+).

### 2. Démarrage du Backend (Drupal 10)
```bash
cd backend
docker compose up -d
```
Le serveur de gestion de données démarre de manière autonome. La base de données MySQL est isolée sur le port `3307` et le site Drupal est accessible sur `http://localhost:8085`. Les données sont sécurisées à l'aide de volumes Docker persistants.

### 3. Démarrage du Frontend (Vue 3 / Vite)
```bash
cd ../frontend
npm install
npm run dev
```
L'application frontend démarre instantanément sur `http://localhost:5173/`.

## 🎛️ Fonctionnalités implémentées
*   **Moteur de recherche réactif combiné** : Filtrage en temps réel (côté client via les propriétés `computed` de Vue) permettant d'effectuer une recherche textuelle simultanée sur le **titre** et sur le **genre/catégorie** des médias.
*   **Filtres cumulatifs** : Possibilité d'affiner l'affichage par type de média (Livres ou Films) et par note minimale (système d'évaluation sur 5 étoiles).
*   **Gestion des états de l'interface** : Intégration d'écrans de chargement dynamiques (*loaders*) lors des requêtes HTTP asynchrones (`fetch`) et gestion des cas d'absence de résultats.

## 🧠 Compétences clés démontrées
*   **DevOps / Systèmes** : Virtualisation et conteneurisation d'environnements, gestion des ports réseaux et résolution des conflits locaux (WampServer).
*   **Ingénierie Backend** : Modélisation de schémas de données relationnels, gestion fine des privilèges d'accès et des permissions selon les rôles utilisateurs.
*   **Développement Frontend Moderne** : Maîtrise du cycle de vie des composants Vue (`onMounted`), programmation réactive asynchrone et intégration du nouveau moteur de styles Tailwind CSS v4.
